## 1. Revisão de consistência

- [x] 1.1 Revisar as specs de `captacao/chamadas`, `captacao/coordenadores` e `captacao/propostas`, confirmando que cada requisito cobre exatamente uma regra descrita na proposta (nenhuma regra do usuário esquecida, nenhum requisito inventado).
- [x] 1.2 Revisar `design.md`, confirmando que o esquema lógico (tabelas e colunas) cobre todos os atributos e restrições exigidos pelas três specs, sem divergência entre os documentos.

## 2. Validação

- [x] 2.1 Rodar `openspec validate modelar-dominio-captacao-propostas --strict` e corrigir qualquer erro de formatação (cenários sem `####`, Purpose abaixo de 50 caracteres, requisito sem cenário).
- [x] 2.2 Confirmar no `design.md` que ficou registrado que a criação efetiva das tabelas (migrations) é responsabilidade de uma change futura de scaffolding do projeto Next.js + PostgreSQL, e não desta change de modelagem.
