# Alerta de Erros via Telegram

> Monitoramento centralizado que notifica falhas de qualquer workflow do N8N diretamente no Telegram, em tempo real

## Problema que resolve

Sem um mecanismo de alerta, uma falha silenciosa em um workflow de produção (ex: um fluxo de importação de vendas ou de envio de mensagens) só seria percebida quando o efeito colateral já tivesse causado impacto — dado não sincronizado, cliente sem resposta, etc. Este fluxo garante visibilidade imediata de qualquer erro, permitindo correção antes que o problema se espalhe.

## Como funciona

1. **Trigger:** Error Trigger — nó especial do N8N que é acionado automaticamente sempre que outro workflow (configurado para usá-lo como tratador de erro) falha
2. **Extração de contexto:** Os dados brutos do erro são organizados em três campos legíveis: URL da execução que falhou, mensagem de erro, e nome do workflow de origem
3. **Notificação:** Uma mensagem formatada é enviada via Telegram com o alerta completo, permitindo agir imediatamente

## Decisões técnicas

- **Decisão:** Um único workflow centralizado de tratamento de erro, reutilizável por qualquer outro fluxo do ambiente, em vez de lógica de alerta duplicada dentro de cada workflow individual.
  **Por quê:** O N8N permite vincular qualquer workflow a um "Error Workflow" central nas configurações. Centralizar evita repetir a mesma lógica de notificação em dezenas de fluxos diferentes e garante que todo erro, de qualquer automação, seja tratado de forma consistente.

- **Decisão:** Telegram como canal de alerta, em vez de e-mail ou log silencioso.
  **Por quê:** Alertas de erro em produção precisam de visibilidade imediata. Uma mensagem de chat gera notificação push instantânea no celular, enquanto e-mail costuma ter latência de checagem maior.

## Tratamento de erros / rate limits

Este é, por definição, o próprio mecanismo de tratamento de erro do ambiente — ele não trata falhas de si mesmo, mas sua simplicidade (poucos nodes, sem dependências externas complexas) minimiza a chance de o próprio alerta falhar silenciosamente.

## Stack

- **Trigger:** Error Trigger (nativo do N8N)
- **Integrações:** Telegram Bot API
- **Destino dos dados:** Mensagem enviada diretamente ao Telegram — sem persistência em banco

---

*Nota: o arquivo `workflow.json` neste diretório foi sanitizado — identificadores de instância e URLs reais do ambiente foram removidos ou mascarados.*
