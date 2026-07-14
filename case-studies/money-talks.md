# MoneyTalks

> Finanças pessoais simplificadas e gamificadas

## Contexto

Entrei neste projeto como desenvolvedor backend. Minha função, além de desenvolver o banco de dados e as automações, era ajudar o cliente a entender conceitos de engenharia de software. O sistema já existia e era todo feito pelo Lovable, sem nenhuma RLS configurada — toda a lógica de regras de negócio e acesso a dados sensíveis estava exposta no front-end.

O MoneyTalks é um SaaS de finanças pessoais onde o usuário pode realizar pagamentos, controlar investimentos, ler notícias, gerar relatórios personalizados com IA, e conversar com um agente de IA dentro do próprio aplicativo, organizado por chats separados. Além disso, há um portal de notícias e um agente de IA no WhatsApp, onde o usuário pode registrar lançamentos financeiros diretamente pela conversa. A stack utilizada é Supabase, N8N e Stripe para pagamentos.

## O desafio técnico

Por ser um sistema que já tinha uma base construída, a maior dificuldade inicial foi compreender toda a arquitetura existente para estruturar o backend do zero — e ao mesmo tempo lidar com mudanças constantes de lógica causadas pelas próprias alterações feitas via Lovable.

## Decisões de arquitetura

- **Decisão:** O agente de IA no WhatsApp só pode criar novas transações — nunca apagar ou atualizar registros existentes.
  **Por quê:** Foi observado que o usuário final poderia dar instruções pouco específicas ao agente, gerando ambiguidade. Restringir a ação a criação evita que uma instrução mal interpretada apague ou altere dados financeiros já existentes.

- **Decisão:** Migrar a geração de relatórios de um workflow do N8N para uma Edge Function do Supabase.
  **Por quê:** A Edge Function respondia mais rápido que o workflow do N8N. Pensando em latência e rate limits, essa foi a melhor decisão arquitetural para essa funcionalidade específica.

- **Decisão consciente de não fazer algo:** O cliente queria que o agente de IA desse dicas de investimento. Recomendei não seguir por esse caminho — isso poderia induzir o usuário a investir em algo específico e gerar problemas, já que a IA ainda comete falhas e não precisa ser aplicada em toda funcionalidade só porque é tecnicamente possível.

## Um obstáculo real

**Problema:** A importação de fatura e conciliação de gastos do cartão de crédito era ineficiente.

**Causa raiz:** Ao importar uma fatura, o usuário nem sempre lançava o valor exato do gasto — o que fazia o sistema duplicar transações, já que a lógica de comparação por nome e valor não conseguia identificar a similaridade entre o lançamento manual e o item da fatura.

**Correção:** A importação de fatura passou a entrar em uma lista de pendências, e o próprio usuário vincula cada item a um gasto já lançado. O que sobra sem vínculo vira uma transação nova, eliminando a duplicação automática.

## Resultado / estado atual

O sistema está em reta final de desenvolvimento, faltando apenas alguns ajustes no front-end. Lançamento previsto para o fim de julho, com aplicativo disponível na App Store.

---

*Nota: nomes de clientes e dados sensíveis foram omitidos ou generalizados por confidencialidade.*
