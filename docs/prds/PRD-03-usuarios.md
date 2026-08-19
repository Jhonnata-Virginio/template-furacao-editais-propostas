# PRD — Usuários

## Objetivo

Manter o cadastro dos membros da equipe que usam o sistema e podem ser coordenadores de proposta, permitindo criar, alterar e inativar usuários sem perder a autoria de ações antigas.

## Usuário e contexto

Qualquer usuário autenticado. Não há níveis de permissão: todos podem administrar o cadastro. O uso é pontual — a equipe é pequena e muda pouco.

## Comportamento esperado

### Listagem

- Tabela com **código interno**, **nome completo**, **e-mail**, **status** (ativo/inativo) e **data de alteração**, ordenada por nome.
- Busca por nome, e-mail ou código interno, e filtro por status (todos / ativos / inativos). Por padrão exibe todos.
- Ações por linha: **Editar** e **Ativar/Inativar**. Ação de topo: **Novo usuário**.
- Estados de carregamento, vazio ("nenhum usuário encontrado") e erro com **Tentar novamente**.

### Formulário (criação e edição, em modal)

- Campos: **código interno** (obrigatório, único), **nome completo** (obrigatório), **e-mail** (obrigatório, único, formato válido), **senha** e **status ativo**.
- Na criação, a senha é obrigatória e confirmada em um segundo campo. Na edição, os campos de senha ficam vazios e opcionais: preenchidos, substituem a senha; em branco, a senha atual é mantida.
- E-mail e código interno duplicados são rejeitados com mensagem no próprio campo, comparando sem diferenciar maiúsculas/minúsculas e ignorando espaços nas extremidades.
- A senha é gravada protegida (hash) e nunca é exibida de volta.
- O sistema mantém **data de criação** e **data de alteração**, atualizadas automaticamente e apenas exibidas.

### Inativação

- Inativar pede confirmação e explica o efeito: o usuário deixa de autenticar e não pode assumir novas propostas em andamento (rascunho ou em preparação).
- O usuário inativo permanece no cadastro e continua aparecendo como autor no histórico das propostas e como coordenador das propostas já submetidas ou arquivadas.
- A inativação é bloqueada, com mensagem que lista as propostas impeditivas, quando o usuário é coordenador de alguma proposta em **rascunho** ou **em preparação**; nesse caso é preciso trocar o coordenador dessas propostas antes.
- Reativar um usuário é permitido a qualquer momento e não pede confirmação.
- Não existe exclusão de usuário, para preservar a autoria de ações antigas.
- O usuário não pode inativar a si mesmo.

## Navegação e integrações

- **De onde vem:** cabeçalho das telas internas.
- **Para onde vai:** permanece na listagem; o bloqueio de inativação oferece link para a proposta impeditiva ([PRD-08](./PRD-08-proposta-ficha.md)).
- **Depende de:** propostas, para verificar vínculos de coordenação antes de inativar.
- **É consumido por:** login ([PRD-01](./PRD-01-login.md)) e seleção de coordenador e responsável no formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)).

## Critérios de aceite

- **Dado que** informei código, nome, e-mail e senha válidos, **quando** salvo um novo usuário, **então** ele aparece na listagem como ativo com as datas de criação e alteração preenchidas.
- **Dado que** informei um e-mail já usado por outro usuário, **quando** tento salvar, **então** o cadastro é recusado com mensagem de e-mail já em uso no campo de e-mail.
- **Dado que** edito um usuário sem preencher a senha, **quando** salvo, **então** os demais dados são atualizados e a senha anterior continua válida.
- **Dado que** um usuário ativo não coordena propostas em rascunho ou em preparação, **quando** confirmo a inativação, **então** ele passa a inativo, deixa de autenticar e não aparece mais na seleção de coordenador.
- **Dado que** um usuário ativo coordena uma proposta em preparação, **quando** tento inativá-lo, **então** a ação é bloqueada com a lista das propostas impeditivas.
- **Dado que** um usuário foi inativado, **quando** abro o histórico de uma proposta antiga registrada por ele, **então** ele continua identificado como autor dos eventos.

## Observações e decisões

- O código interno é informado pela equipe (não gerado pelo sistema), porque já existe uma numeração usada no laboratório.
- Não há campo de perfil, papel ou permissão: qualquer usuário ativo executa qualquer operação, conforme decisão do minimundo.
- A troca de senha de outra pessoa é permitida por simplicidade, dada a confiança e o tamanho da equipe; não há recuperação por e-mail no MVP.
