## 1. Registro da decisão

- [x] 1.1 Editar `docs/Constituicao/tech-stack.md` adicionando a stack aprovada: Next.js full-stack (front + back no mesmo projeto, via App Router/Route Handlers/Server Actions) e PostgreSQL como banco relacional.
- [x] 1.2 Registrar no mesmo documento as alternativas avaliadas e rejeitadas (Java, back-end separado em Node/Python, SQLite/libSQL), com o racional resumido de cada rejeição, conforme `design.md` - Decisions.

## 2. Validação

- [x] 2.1 Revisar `docs/Constituicao/tech-stack.md` conferindo que reflete exatamente as decisões de `proposal.md` e `design.md` (sem inconsistência entre os documentos).
- [x] 2.2 Rodar `openspec validate adotar-stack-nextjs-postgresql --strict` e confirmar que a change passa a validação antes do archive.
