# [Agente whatsapp]

> [esse fluxo recebe um webhook da api não oficial do Whhatsapp, passa por um buffer de mensagem, para concatenar mensagens picadas, enviar a llmm, que com a base de conhecimento gera uma resposta que é quebrada para ficar mais humanizada e enviar a resposta.]

## Como funciona

[Descrição em linguagem simples do fluxo, passo a passo — não é preciso detalhar cada node, só a lógica geral.]

1. **Trigger:** [o que inicia o fluxo — webhook, schedule, evento de outro sistema]
Mensagem no whatsapp
2. **[Etapa principal 1]:**a  emnsagem passa p
3. **[Etapa principal 2]:** [o que acontece]
4. **Saída:** [onde o resultado vai parar — banco, outra API, notificação, etc]

## Decisões técnicas

[Pontos onde você escolheu entre alternativas — isso é o que mais interessa pra quem lê o portfólio.]

- **Decisão:** [ex: node de refresh automático de token antes de cada chamada, em vez de renovar só quando expira]
  **Por quê:** [trade-off considerado]

## Tratamento de erros / rate limits

[Como o fluxo lida com falhas — retry, alertas, fallback. Se a API externa tem rate limit, como isso foi respeitado.]

## Stack

- **Trigger:** [tipo]
- **Integrações:** [APIs externas envolvidas]
- **Destino dos dados:** [ex: Supabase, planilha, outro sistema]

---

*Nota: o arquivo `workflow.json` neste diretório foi sanitizado — credenciais, tokens e URLs sensíveis foram substituídos por placeholders.*
