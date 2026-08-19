## Purpose

A proposta é a entidade central da captação: liga uma chamada a um coordenador e registra o que foi preparado, enviado e decidido. Este documento define as regras de cadastro, consulta, alteração e exclusão que preservam a integridade dos dados e o histórico da submissão.

## ADDED Requirements

### Requirement: Cadastro exige chamada vinculada e dados obrigatórios
O cadastro da proposta SHALL exigir uma chamada vinculada e os demais dados obrigatórios (coordenador responsável, título e demais informações definidas para a proposta) preenchidos.

#### Scenario: Cadastro completo e com chamada vinculada
- **WHEN** uma proposta é cadastrada com todos os dados obrigatórios preenchidos e uma chamada vinculada
- **THEN** o sistema aceita o cadastro, com a proposta em status rascunho

#### Scenario: Cadastro sem chamada vinculada
- **WHEN** uma proposta é cadastrada sem indicar uma chamada
- **THEN** o sistema rejeita o cadastro

#### Scenario: Cadastro com dado obrigatório ausente
- **WHEN** uma proposta é cadastrada faltando algum dado obrigatório
- **THEN** o sistema rejeita o cadastro e indica o dado faltante

### Requirement: Estados da proposta
A proposta SHALL estar sempre em um dos três status possíveis: rascunho, em preparação ou submetida. Toda proposta nova SHALL nascer em rascunho.

#### Scenario: Proposta nasce em rascunho
- **WHEN** uma proposta é cadastrada com sucesso
- **THEN** ela é registrada com status rascunho

#### Scenario: Tentativa de status inválido
- **WHEN** uma proposta é cadastrada ou alterada com um status fora do conjunto rascunho/em preparação/submetida
- **THEN** o sistema rejeita a operação

### Requirement: Numeração sequencial anual na submissão
Ao ser submetida, a proposta SHALL receber um número de submissão no formato `XXX-YYYY`, onde `XXX` é a posição sequencial da proposta dentro do ano (com zero à esquerda, começando em `001`) e `YYYY` é o ano vigente da submissão. Esse número SHALL ser único e SHALL NOT se repetir entre propostas do mesmo ano.

#### Scenario: Submissão recebe número sequencial no formato XXX-YYYY
- **WHEN** uma proposta em preparação é submetida e é a primeira submissão do ano vigente
- **THEN** o sistema atribui a ela o número `001-YYYY` (com `YYYY` igual ao ano vigente) e muda seu status para submetida

#### Scenario: Números não se repetem no mesmo ano
- **WHEN** duas propostas distintas são submetidas dentro do mesmo ano
- **THEN** cada uma recebe um número sequencial diferente dentro do formato `XXX-YYYY`, por exemplo `001-2026` e `002-2026`

#### Scenario: Sequência reinicia em ano novo
- **WHEN** a primeira proposta de um novo ano é submetida
- **THEN** o sistema atribui a ela a posição `001` combinada com o novo ano, mesmo que o ano anterior tenha chegado a uma posição mais alta

#### Scenario: Proposta não submetida não possui número de submissão
- **WHEN** uma proposta está em rascunho ou em preparação
- **THEN** ela não possui número de submissão

### Requirement: Consulta por proposta, chamada, coordenador ou título
A consulta SHALL permitir localizar propostas por número/identificação da proposta, pela chamada vinculada, pelo coordenador responsável ou pelo título.

#### Scenario: Busca por qualquer critério suportado
- **WHEN** uma busca é realizada informando proposta, chamada, coordenador ou título
- **THEN** o sistema retorna as propostas correspondentes ao critério informado

#### Scenario: Busca sem correspondência
- **WHEN** uma busca é realizada com um critério que não corresponde a nenhuma proposta
- **THEN** o sistema retorna uma lista vazia, sem erro

### Requirement: Ficha da proposta
A ficha da proposta SHALL exibir os dados gerais da proposta, seu tipo e o histórico simples da tentativa (o que foi preparado, enviado e decidido).

#### Scenario: Abertura da ficha
- **WHEN** a ficha de uma proposta é aberta
- **THEN** o sistema exibe os dados gerais, o tipo e o histórico simples da tentativa

### Requirement: Alteração preserva a validade da submissão
A alteração SHALL permitir corrigir os dados da proposta respeitando as mesmas regras do cadastro, mas SHALL NOT alterar o número de submissão (`XXX-YYYY`) nem o status já consolidado como submetida.

#### Scenario: Alteração de dados antes da submissão
- **WHEN** uma proposta em rascunho ou em preparação tem seus dados gerais corrigidos
- **THEN** o sistema aceita a alteração, mantendo as mesmas regras de obrigatoriedade do cadastro

#### Scenario: Tentativa de alterar submissão consolidada
- **WHEN** uma alteração tenta modificar o número de submissão ou reverter o status de uma proposta já submetida
- **THEN** o sistema rejeita a alteração

### Requirement: Exclusão restrita a rascunho e com confirmação
A exclusão SHALL ser permitida apenas para propostas em status rascunho, e SHALL exigir confirmação explícita antes de ser efetivada.

#### Scenario: Exclusão de rascunho confirmada
- **WHEN** a exclusão de uma proposta em rascunho é confirmada
- **THEN** o sistema remove a proposta

#### Scenario: Tentativa de exclusão fora do rascunho
- **WHEN** a exclusão é solicitada para uma proposta em preparação ou submetida
- **THEN** o sistema rejeita a exclusão

#### Scenario: Exclusão sem confirmação
- **WHEN** a exclusão de uma proposta em rascunho é solicitada sem confirmação
- **THEN** o sistema não efetiva a exclusão e aguarda a confirmação
