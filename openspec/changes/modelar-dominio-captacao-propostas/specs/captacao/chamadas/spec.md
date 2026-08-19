## Purpose

Define e mantém o ciclo de vida da chamada (edital) que abre a captação de propostas, garantindo que seu código seja único, que suas datas sejam coerentes e que seu status reflita corretamente se ela ainda aceita propostas.

## ADDED Requirements

### Requirement: Código único da chamada
Cada chamada SHALL possuir um código que a identifica de forma única. O sistema SHALL rejeitar o cadastro de uma chamada cujo código já pertença a outra chamada existente.

#### Scenario: Cadastro com código inédito
- **WHEN** uma chamada é cadastrada com um código que ainda não existe
- **THEN** o sistema aceita e persiste a chamada

#### Scenario: Cadastro com código duplicado
- **WHEN** uma chamada é cadastrada com um código já usado por outra chamada
- **THEN** o sistema rejeita o cadastro e nenhuma chamada nova é criada

### Requirement: Coerência entre abertura e encerramento
A data de encerramento da chamada SHALL ser igual ou posterior à data de abertura.

#### Scenario: Encerramento posterior à abertura
- **WHEN** uma chamada é cadastrada com data de encerramento posterior à data de abertura
- **THEN** o sistema aceita o cadastro

#### Scenario: Encerramento anterior à abertura
- **WHEN** uma chamada é cadastrada ou alterada com data de encerramento anterior à data de abertura
- **THEN** o sistema rejeita a operação e informa a inconsistência de datas

### Requirement: Dados obrigatórios da chamada
O cadastro da chamada SHALL exigir agência, título, data de abertura, data de encerramento, valor teto e limites por rubrica preenchidos.

#### Scenario: Cadastro com todos os dados obrigatórios
- **WHEN** uma chamada é cadastrada com agência, título, datas, valor teto e limites por rubrica preenchidos
- **THEN** o sistema aceita o cadastro

#### Scenario: Cadastro com dado obrigatório ausente
- **WHEN** uma chamada é cadastrada faltando algum dos dados obrigatórios
- **THEN** o sistema rejeita o cadastro e indica o dado faltante

### Requirement: Status restrito da chamada
A chamada SHALL estar sempre em um dos três status possíveis: aberta, encerrada ou cancelada. O sistema SHALL rejeitar qualquer valor de status fora desse conjunto.

#### Scenario: Chamada nasce aberta
- **WHEN** uma chamada é cadastrada com sucesso
- **THEN** ela é registrada com status aberta

#### Scenario: Tentativa de status inválido
- **WHEN** uma chamada é cadastrada ou alterada com um status fora do conjunto aberta/encerrada/cancelada
- **THEN** o sistema rejeita a operação
