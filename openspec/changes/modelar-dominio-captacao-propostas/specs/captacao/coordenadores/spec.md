## Purpose

Define o cadastro do coordenador, a pessoa que responde por propostas na captação, garantindo que o e-mail identifique o acesso de forma única e que a inativação preserve o histórico de autoria sem permitir novas responsabilidades pendentes.

## ADDED Requirements

### Requirement: E-mail único como identificador de acesso
O e-mail do coordenador SHALL identificar seu acesso e SHALL ser único entre todos os coordenadores cadastrados.

#### Scenario: Cadastro com e-mail inédito
- **WHEN** um coordenador é cadastrado com um e-mail que ainda não existe no cadastro
- **THEN** o sistema aceita o cadastro

#### Scenario: Cadastro com e-mail duplicado
- **WHEN** um coordenador é cadastrado ou alterado com um e-mail já usado por outro coordenador
- **THEN** o sistema rejeita a operação

### Requirement: Dados obrigatórios do coordenador
O cadastro do coordenador SHALL exigir código interno, nome completo, e-mail e senha protegida preenchidos.

#### Scenario: Cadastro com todos os dados obrigatórios
- **WHEN** um coordenador é cadastrado com código interno, nome completo, e-mail e senha preenchidos
- **THEN** o sistema aceita o cadastro e registra as datas de criação

#### Scenario: Cadastro com dado obrigatório ausente
- **WHEN** um coordenador é cadastrado faltando algum dos dados obrigatórios
- **THEN** o sistema rejeita o cadastro e indica o dado faltante

### Requirement: Proteção da senha do coordenador
A senha do coordenador SHALL ser armazenada de forma protegida e NUNCA SHALL ser exibida em texto plano em consultas, fichas ou listagens.

#### Scenario: Consulta não expõe a senha
- **WHEN** os dados de um coordenador são consultados ou exibidos em ficha
- **THEN** a senha não aparece em texto plano em nenhum campo retornado

### Requirement: Preservação de autoria após inativação
Um coordenador inativado SHALL permanecer no cadastro, e as propostas e ações já registradas em seu nome SHALL continuar associadas a ele.

#### Scenario: Inativação preserva o histórico
- **WHEN** um coordenador com propostas já registradas é inativado
- **THEN** as propostas e ações anteriores continuam associadas a esse coordenador, sem perda de autoria

### Requirement: Coordenador inativo não assume novas propostas pendentes
Um coordenador inativo SHALL NOT poder ser atribuído como responsável de uma nova proposta pendente (rascunho ou em preparação).

#### Scenario: Tentativa de atribuir proposta pendente a coordenador inativo
- **WHEN** uma nova proposta pendente é cadastrada ou alterada indicando um coordenador inativo como responsável
- **THEN** o sistema rejeita a operação

#### Scenario: Coordenador ativo assume proposta pendente
- **WHEN** uma nova proposta pendente é cadastrada indicando um coordenador ativo como responsável
- **THEN** o sistema aceita a operação
