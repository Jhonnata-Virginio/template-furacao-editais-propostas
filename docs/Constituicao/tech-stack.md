# Stack tecnológica

Este documento é a fonte de verdade para as tecnologias adotadas no projeto. Registre somente escolhas já aprovadas; não presuma ferramentas, versões ou serviços.

## Stack aprovada

- **Front-end e back-end:** Next.js, em um único projeto full-stack (App Router, Route Handlers e Server Actions cobrem as necessidades de back-end, sem serviço de API separado).
- **Banco relacional:** PostgreSQL.

Racional: o sistema é de porte pequeno e a prioridade é velocidade de desenvolvimento com infraestrutura mínima. Um único projeto Next.js reduz superfície de deploy e manutenção; PostgreSQL suporta múltiplas conexões concorrentes e não impõe restrição de topologia de deploy.

## Alternativas avaliadas e rejeitadas

- **Java (ex.: Spring Boot):** rejeitado pelo overhead de setup, build e verbosidade, incompatível com a meta de desenvolvimento rápido para um sistema pequeno.
- **Back-end separado (ex.: NestJS, FastAPI):** rejeitado por duplicar deploy e superfície de manutenção sem ganho relevante para o porte deste sistema; a separação de responsabilidades que traria só se paga com um time grande ou domínio complexo.
- **SQLite / libSQL (Turso):** rejeitado como escolha principal por dois motivos — exige um serviço de réplica remota (libSQL/Turso) para funcionar bem em deploy serverless/edge, e trava escritas concorrentes (um writer por vez), o que é uma restrição a menos com PostgreSQL.

Detalhes e trade-offs completos: `openspec/changes/adotar-stack-nextjs-postgresql/design.md` (ou o arquivo correspondente após archive).
