# Agente de Atendimento via WhatsApp (Multimodal + RAG)

> Agente de IA no WhatsApp que atende clientes de uma loja, respondendo texto, áudio, imagem e PDF com base em uma fonte de conhecimento própria (RAG)

## Problema que resolve

Substitui o atendimento manual de primeiro nível no WhatsApp, respondendo dúvidas recorrentes sobre produtos, políticas de troca/reembolso e informações da loja de forma automática — 24/7 — enquanto mantém uma rota clara de escalonamento para um atendente humano quando o assunto foge do escopo do agente ou o cliente pede urgência.

## Como funciona

1. **Trigger:** Webhook que recebe eventos de mensagem da Z-API (WhatsApp)
2. **Filtro de ruído:** Descarta newsletters, mensagens de grupo, broadcasts e mensagens encaminhadas — só processa conversa direta e genuína
3. **Detecção de intervenção humana:** Se a mensagem foi enviada pelo próprio atendente (`fromMe`), o agente de IA é desativado por um período (TTL configurável no Redis), evitando que a IA responda por cima de um humano já atendendo a conversa
4. **Roteamento por tipo de mensagem:** Um Switch identifica se o conteúdo é texto, áudio, imagem ou PDF e direciona para o processamento correto:
   - Áudio → transcrição via Whisper (OpenAI)
   - Imagem → descrição via GPT-4o-mini (vision)
   - PDF → extração de texto
   - Texto → segue direto
5. **Buffer de mensagens (debounce):** Cada mensagem processada é empilhada em uma lista no Redis. O fluxo aguarda um tempo configurável e só segue adiante quando o buffer para de crescer — evitando que o agente responda a cada mensagem picotada quando o usuário está digitando em várias partes
6. **Identificação/criação de contato:** Busca o usuário no Supabase pelo número de WhatsApp; se não existir, cria um novo registro
7. **Agente de IA com RAG:** O agente responde exclusivamente com base em uma base de conhecimento vetorizada (Supabase Vector Store), com memória de conversa persistida no Postgres por sessão
8. **Formatação de saída:** Uma segunda chamada de LLM quebra a resposta do agente em múltiplas mensagens curtas e naturais (formato de conversa real do WhatsApp, não um bloco único de texto), validada por um parser estruturado
9. **Envio:** As mensagens formatadas são enviadas em sequência via Z-API, com espaçamento entre elas para simular digitação humana

## Decisões técnicas

- **Decisão:** Buffer de mensagens no Redis com debounce, em vez de responder a cada mensagem recebida individualmente.
  **Por quê:** Usuários no WhatsApp costumam mandar uma ideia dividida em várias mensagens seguidas. Sem o buffer, o agente responderia prematuramente à primeira parte, fora de contexto.

- **Decisão:** Verificação de `fromMe` para desativar o agente temporariamente quando um humano assume a conversa.
  **Por quê:** Evita que o agente de IA e um atendente humano respondam simultaneamente ao mesmo cliente, gerando confusão. O tempo de desativação é armazenado com TTL no Redis, voltando a ativar o agente automaticamente depois de um período de inatividade do atendente.

- **Decisão:** Resposta do agente restrita ao conteúdo recuperado via RAG, com instrução explícita para nunca inventar dados quando a informação não estiver disponível.
  **Por quê:** Prioriza confiabilidade sobre naturalidade — o agente prefere escalonar para um humano a arriscar uma alucinação sobre política de troca, preço ou disponibilidade.

- **Decisão:** Segunda chamada de LLM só para reformatar a saída em múltiplas mensagens curtas, separada da geração da resposta em si.
  **Por quê:** Separar "o que responder" de "como fragmentar a resposta" simplifica o prompt principal do agente e garante que a quebra de mensagens siga um formato validado (JSON estruturado), reduzindo risco de mensagens longas e artificiais no WhatsApp.

## Tratamento de erros / rate limits

Mensagens em formato não suportado caem em um branch de erro dedicado (`fallbackOutput`), que insere uma mensagem padrão de erro no buffer em vez de quebrar o fluxo. O envio final ao WhatsApp acontece em loop item por item, com espera entre envios — isso também ajuda a respeitar o rate limit da Z-API ao evitar rajadas de requisições simultâneas.

## Stack

- **Trigger:** Webhook (Z-API)
- **Integrações:** Z-API (WhatsApp), OpenAI (chat, transcrição de áudio, visão, embeddings), Supabase (dados de contato, memória de conversa, vector store para RAG), Redis (buffer de mensagens e controle de status)
- **Destino dos dados:** Supabase (contatos e histórico), resposta final entregue ao usuário via WhatsApp

---

*Nota: o arquivo `workflow.json` neste diretório foi sanitizado — credenciais, tokens, números de telefone e identificadores de instância foram removidos ou substituídos por placeholders.*
