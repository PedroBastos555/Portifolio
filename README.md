# Pedro Bastos

Sou formado em Análise e Desenvolvimento de Sistemas, cursando pós-graduação em IA Aplicada a Negócios. Tenho experiência com projetos de automação e desenvolvimento com ferramentas de IA. Atuo também como consultor de tecnologia para pequenas empresas.

**Stack principal:** Python, Java, Git, GitHub, TypeScript, Supabase (Postgres/RLS), N8N, OpenAI API, Stripe, Redis
**Ferramentas de desenvolvimento:** Claude Code (lógica/estrutura), Lovable (camada visual), Supabase MCP (operações de banco), N8N (automações), Postman (teste de API)

---

## Projetos

### 🔹 Tavares Delta Hub

Plataforma multi-tenant de gestão financeira e e-commerce, construída para um cliente que tem uma consultoria financeira.

- Gestão de contas inteligente, com módulo de cotação onde o cliente cria uma lista de compras, seleciona fornecedores e compara valores para chegar ao melhor orçamento — os itens são separados por fornecedor e as cotações antigas ficam salvas para o usuário acompanhar a variação de preço de cada item ao longo do tempo. O módulo de cotação é integrado ao controle de estoque, com aviso de estoque mínimo.
- Integração com Mercado Livre para importação automática de vendas, controle de devoluções e métricas relevantes.
- Arquitetura de dupla camada: área admin + área do cliente, com RLS por `company_id`.

📄 [Case study completo](https://github.com/PedroBastos555/Portifolio/blob/main/case-studies/tavares-delta.md) *(nomes de clientes e dados sensíveis omitidos por confidencialidade)*

### 🔹 MoneyTalks

SaaS de finanças pessoais com IA.

- Agente de IA no WhatsApp que realiza lançamentos, consultas, faz conciliação de extrato bancário com os lançamentos e dá dicas financeiras com base no perfil do usuário.
- Assinatura via Stripe (modo live).
- Pipeline RAG completo: chunking de base de conhecimento + busca por similaridade (`match_kb_chunks`) para respostas contextualizadas.
- Auditoria de qualidade de prompt e auditoria de segurança.
- Portal de notícias segmentado por tema, automatizado via N8N com API de notícias.

📄 [Case study completo](https://github.com/PedroBastos555/Portifolio/blob/main/case-studies/money-talks.md)

---

## Automações / Integrações

- **Agente de IA para WhatsApp (https://github.com/PedroBastos555/Portifolio/tree/main/n8n-workflow/agente%20whatsapp)** — conecta ao WhatsApp do usuário utilizando a API não oficial Z-API, com suporte a texto, áudio, imagem e PDF.
- **Portal de notícias automatizado (https://github.com/PedroBastos555/Portifolio/tree/main/n8n-workflow/portal_noticias)** — busca notícias filtrando por nicho e adiciona ao banco de dados.
- **Avaliador e refinador de prompt (https://github.com/PedroBastos555/Portifolio/tree/main/n8n-workflow/refinador_prompt)** — lê um prompt, pontua com base em critérios de engenharia de prompt e devolve uma versão otimizada.
- **Tratamento de erro (https://github.com/PedroBastos555/Portifolio/tree/main/n8n-workflow/tratamento_erro)** - workflow que dispara quando um fluxo da erro e envia um aviso direto no telegram
---

## Contato

- LinkedIn: Pedro Bastos (https://www.linkedin.com/in/pedro-bastos-28818b271)
- Email: pedrogabryel1604@gmail.com
