## Why

O sistema ainda não tem nenhuma entidade de domínio registrada. Antes de qualquer tela ou implementação, é preciso fixar o que o sistema de captação de propostas representa — chamada, proposta e coordenador — e as regras de negócio que protegem a integridade desses dados (unicidade, vínculos obrigatórios, transições de status, numeração de submissão).

## What Changes

- Introduz a entidade **Chamada**: código único, agência, título, datas de abertura/encerramento, valor teto e limites por rubrica; com a regra de que o encerramento não pode ser anterior à abertura; status aberta, encerrada ou cancelada.
- Introduz a entidade **Coordenador**: código interno, nome completo, e-mail (identificador de acesso, único), senha protegida, status ativo/inativo e datas de criação/alteração; coordenador inativo permanece no cadastro para preservar autoria, mas não pode assumir novas propostas pendentes.
- Introduz a entidade **Proposta**, central do sistema: liga uma chamada a um coordenador; guarda título, resumo, duração, responsável, observações internas e orçamento; ao ser submetida, recebe um número sequencial anual que nunca se repete; status rascunho, em preparação ou submetida.
- Define as regras de cadastro, consulta, ficha, alteração e exclusão da proposta: cadastro exige dados obrigatórios e chamada vinculada; consulta busca por proposta, chamada, coordenador ou título; ficha mostra dados gerais, tipo e histórico simples; alteração respeita as regras do cadastro sem alterar a validade da submissão; exclusão só é permitida em rascunho e exige confirmação.

## Capabilities

### New Capabilities
- `captacao/chamadas`: ciclo de vida e regras de integridade da chamada (datas, status, unicidade de código).
- `captacao/coordenadores`: cadastro e regras de acesso do coordenador (unicidade de e-mail, ativação/inativação, preservação de autoria).
- `captacao/propostas`: cadastro, consulta, alteração e exclusão da proposta, incluindo vínculo com chamada e coordenador e numeração sequencial de submissão.

### Modified Capabilities
(nenhuma — não há capabilities existentes no projeto)

## Impact

- Banco de dados relacional (PostgreSQL, ver `docs/Constituicao/tech-stack.md`): novas tabelas/entidades para chamada, proposta e coordenador, com suas restrições de integridade.
- Não inclui, nesta change: telas, autenticação, fluxo de aprovação/decisão da proposta pela chamada, ou geração de relatórios — ficam para changes futuras que dependerão deste modelo de domínio.
