## Why

O sistema é pequeno e a prioridade é velocidade de desenvolvimento com o mínimo de infraestrutura para manter. Ainda não havia uma stack técnica registrada em `docs/Constituicao/tech-stack.md`; sem essa decisão fixada, cada change corre o risco de introduzir tecnologias divergentes.

## What Changes

- Adota **Next.js** como stack full-stack única: front-end e back-end (API/rotas de servidor) no mesmo projeto, via App Router, Route Handlers e Server Actions.
- Adota **PostgreSQL** como banco relacional do projeto.
- Descarta explicitamente as alternativas avaliadas: Java (rejeitado por trazer overhead de desenvolvimento incompatível com o porte do sistema), backend separado em outra linguagem/framework (rejeitado por duplicar deploy e superfície de manutenção sem ganho relevante para um sistema pequeno) e SQLite/libSQL (rejeitado como escolha principal pelo risco de incompatibilidade com deploy serverless/edge e pela limitação de escrita concorrente).
- Registra a decisão e o racional em `docs/Constituicao/tech-stack.md`, que passa a ser a fonte de verdade para a stack do projeto.

## Capabilities

### New Capabilities

(nenhuma — esta change não introduz nem altera comportamento de tela ou fluxo)

### Modified Capabilities

(nenhuma — decisão de stack técnica, sem mudança de requisito de comportamento; `skip_specs: true` definido em `.openspec.yaml`)

## Impact

- `docs/Constituicao/tech-stack.md`: passa a registrar a stack aprovada (Next.js full-stack + PostgreSQL) e as alternativas rejeitadas.
- Futuras changes de implementação (scaffolding do projeto, ORM, configuração de banco, deploy) devem seguir essa decisão como restrição já aprovada.
