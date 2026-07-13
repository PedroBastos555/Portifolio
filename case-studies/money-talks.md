# [Nome do Projeto]

> [Uma frase de posicionamento — o que é e para quem]

## Contexto

[2-3 frases sobre o problema de negócio, não técnico ainda. Quem tinha esse problema e por que era doloroso.]

## O desafio técnico

[O que tornava isso não-trivial: restrições, volume, integração com sistemas externos, requisitos de segurança/compliance, prazo, etc. Aqui você já pode citar tecnicalidades.]

## Decisões de arquitetura

[O coração do case study. Não descreva o que usou — descreva o que escolheu entre alternativas e por quê.]

- **Decisão:** [ex: RLS por company_id em vez de banco por tenant]
  **Por quê:** [trade-off considerado]

- **Decisão:** [ex: RPC de settlement em lote]
  **Por quê:** [trade-off considerado]

- **Decisão consciente de não fazer algo:** [ex: período de lock ficou sem seletor de ano nesta fase — decisão do cliente/priorização]

## Um obstáculo real

[Um bug ou problema específico e como foi resolvido. Não precisa ser dramático — mostra rigor técnico e capacidade de debugar, não perfeição.]

**Problema:** [o que quebrou ou que risco foi identificado]
**Causa raiz:** [o que realmente estava acontecendo]
**Correção:** [o que foi feito]

## Resultado / estado atual

[Métricas reais, sem invenção: número de módulos, funcionalidades em produção, o que está em progresso. Se não tiver métrica de negócio (receita, tempo economizado), tudo bem — estado técnico honesto já é suficiente.]

---

*Nota: nomes de clientes e dados sensíveis foram omitidos ou generalizados por confidencialidade.*
