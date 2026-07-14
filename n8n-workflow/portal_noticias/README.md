# Curadoria Automática de Notícias Financeiras

> Pipeline agendado que busca, filtra e classifica notícias de economia/finanças por relevância antes de publicá-las no app

## Problema que resolve

Alimentar o portal de notícias do produto exigiria curadoria manual constante ou, na outra ponta, publicar tudo que uma API de notícias retorna — incluindo conteúdo irrelevante para o público final (leitor comum, não investidor profissional). Este fluxo automatiza a triagem, garantindo que só notícias com relevância real para finanças pessoais cheguem ao usuário.

## Como funciona

1. **Trigger:** Agendamento a cada 12 horas
2. **Definição de parâmetros:** Monta a janela de busca (dia anterior, ajustada por timezone), a pontuação mínima de corte e os termos de pesquisa (finanças, economia, investimento, juros, inflação, emprego, etc.)
3. **Busca de notícias:** Consulta a NewsAPI filtrando por idioma português, data, e uma lista de domínios de veículos brasileiros confiáveis (InfoMoney, Valor, Exame, G1, UOL, Folha, Estadão, CNN Brasil, Terra)
4. **Separação em itens:** Divide o array de artigos retornado em itens individuais para processar um a um
5. **Validação de campos obrigatórios:** Descarta artigos sem título válido antes de gastar uma chamada de IA analisando-os
6. **Classificação por IA:** Um agente (Claude Haiku) avalia cada notícia e retorna uma pontuação de relevância (0–10) e uma categoria fixa (selic, dólar, inflação, economia ou mercado), com critérios de pontuação explícitos e exemplos de notícias boas/medianas/ruins no prompt
7. **Filtro por pontuação mínima:** Só segue adiante quem atinge o score mínimo configurado
8. **Persistência:** Notícias aprovadas são inseridas na tabela `news` do Supabase, prontas para exibição no app

## Decisões técnicas

- **Decisão:** Classificação de relevância feita por IA com categorias fixas e pontuação numérica, em vez de aceitar todo o retorno da NewsAPI.
  **Por quê:** Uma API de notícias por palavra-chave traz muito ruído (ex: notícias técnicas de fintech, termos técnicos de mercado internacional) que não interessa ao público leigo do produto. Delegar a triagem para um modelo de linguagem, com critérios explícitos e exemplos calibrados no prompt, permite escalar a curadoria sem revisão manual diária.

- **Decisão:** Categorias fechadas e em minúsculas, definidas em uma lista curta e específica (selic, dolar, inflacao, economia, mercado), em vez de deixar o modelo categorizar livremente.
  **Por quê:** Categorias abertas geram inconsistência (variações de grafia, sinônimos) que quebram filtros no frontend. Fechar o vocabulário evita esse problema estruturalmente.

- **Decisão:** Validação de campos obrigatórios (`title` existente e não nulo) antes de enviar o artigo para análise de IA.
  **Por quê:** Evita gastar chamadas de IA (custo e tempo) em itens malformados que a própria NewsAPI eventualmente retorna incompletos.

## Tratamento de erros / rate limits

Os nodes de separação de itens e de inserção no banco usam `continueRegularOutput` em caso de erro, o que evita que uma falha pontual em um único artigo interrompa o processamento do lote inteiro. O agendamento a cada 12 horas (em vez de tempo real) também mantém o volume de chamadas à NewsAPI e ao modelo de IA dentro de um padrão previsível.

## Stack

- **Trigger:** Schedule Trigger (a cada 12h)
- **Integrações:** NewsAPI (busca de notícias), Anthropic Claude Haiku (classificação de relevância e categoria), Supabase (persistência)
- **Destino dos dados:** Tabela `news` no Supabase

---

*Nota: o arquivo `workflow.json` neste diretório foi sanitizado — identificadores de instância foram removidos.*
