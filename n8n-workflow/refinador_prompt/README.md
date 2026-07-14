# Pipeline de Avaliação e Otimização de Prompts (Meta-Agente)

> Sistema de dois agentes em cadeia que avalia a qualidade de um prompt de IA e gera automaticamente uma versão otimizada e pronta para produção

## Problema que resolve

Escrever prompts de produção robustos (com guardrails de segurança, escopo bem definido e proteção contra manipulação) é um processo manual e repetitivo. Esse fluxo transforma a criação de prompts em um pipeline estruturado: um agente audita o prompt recebido contra critérios fixos, e um segundo agente aplica as correções identificadas, gerando um prompt novo pronto para uso — sem que o cliente precise saber engenharia de prompt.

## Como funciona

1. **Trigger:** Chat trigger (interface de conversa)
2. **Extração de dados:** Uma LLM Chain interpreta a mensagem livre do usuário e separa dois elementos estruturados: o prompt atual a ser avaliado e a solicitação de mudança desejada — devolvendo um JSON estrito
3. **Parsing estruturado:** Os campos extraídos (`prompt_atual`, `solicitação`) são organizados em variáveis nomeadas para uso nos próximos nodes
4. **Agente Avaliador ("Analista de prompt"):** Audita o prompt recebido contra uma rubrica fixa de 0 a 10 pontos (persona, escopo, guardrails obrigatórios, formato de saída, tom, clareza), verifica a presença de 4 guardrails de segurança obrigatórios, e retorna um relatório estruturado com nota, pontos positivos, problemas por severidade (ALTA/MÉDIA/BAIXA) e mudanças necessárias
5. **Preparação para o próximo agente:** O relatório e o prompt original são organizados para alimentar o agente seguinte
6. **Agente Otimizador ("Agente otimizador"):** Recebe o relatório de auditoria e o prompt original, e reescreve o prompt aplicando as correções na ordem de uma hierarquia de prioridades fixa (Segurança → Escopo → Persona → Formatação), retornando apenas o prompt novo, pronto para produção
7. **Saída final:** Um node de formatação concatena o relatório de avaliação e o prompt otimizado em uma única mensagem de resposta ao usuário

## Decisões técnicas

- **Decisão:** Separar a avaliação (Agente Avaliador) da reescrita (Agente Otimizador) em dois agentes distintos, em vez de um único agente fazer as duas coisas.
  **Por quê:** Cada etapa tem uma responsabilidade única e um formato de saída diferente (relatório estruturado vs. prompt final em texto livre). Separar reduz a chance do modelo misturar diagnóstico com solução, e permite depurar cada etapa isoladamente.

- **Decisão:** Ambos os agentes recebem uma instrução explícita de proteção contra prompt injection, ignorando qualquer instrução embutida dentro do conteúdo que estão analisando.
  **Por quê:** Como a própria função do sistema é processar prompts de terceiros — que podem conter tentativas deliberadas de manipulação —, essa camada de proteção é essencial: sem ela, um prompt malicioso enviado para avaliação poderia sequestrar o comportamento do próprio agente avaliador.

- **Decisão:** Rubrica de avaliação com pontuação fixa por critério (não uma nota subjetiva "no olho") e exemplo de input/output completo embutido no prompt do Agente Avaliador.
  **Por quê:** Notas subjetivas geram inconsistência entre execuções. Fixar os pesos de cada critério e fornecer um exemplo completo (few-shot) deixa o comportamento do modelo mais determinístico e replicável.

- **Decisão:** O Agente Otimizador retorna apenas o prompt final — sem changelog, sem comentários — mesmo tendo acesso ao relatório de problemas.
  **Por quê:** O produto final para o cliente é um prompt pronto para colar e usar. Expor o raciocínio interno da otimização (marcadores como "[OTIMIZADOR: assunção feita]") polui a saída; o prompt precisa estar limpo para uso imediato em produção.

## Tratamento de erros / rate limits

Os agentes principais (Analista de Prompt e Agente Otimizador) têm `retryOnFail` habilitado, garantindo nova tentativa automática em caso de falha transitória da API do modelo, sem quebrar a execução do fluxo completo.

## Stack

- **Trigger:** Chat Trigger (n8n)
- **Integrações:** OpenAI (GPT-5 mini, usado tanto na extração de dados quanto nos dois agentes)
- **Memória:** Buffer de janela (memória de curto prazo) individual para cada agente, mantendo contexto de conversa
- **Destino dos dados:** Resposta direta ao usuário via chat — sem persistência em banco

---

*Nota: o arquivo `workflow.json` neste diretório foi sanitizado — identificadores de instância foram removidos.*
