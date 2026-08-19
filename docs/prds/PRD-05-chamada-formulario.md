# PRD — Formulário de chamada

## Objetivo

Registrar e corrigir os dados de uma chamada pública: identificação, prazos, valor teto e a lista de documentos obrigatórios que as propostas daquela chamada precisarão anexar.

## Usuário e contexto

Qualquer usuário autenticado, normalmente ao identificar um novo edital. Os dados aqui informados definem as regras que o sistema aplicará às propostas vinculadas, por isso é a tela que exige mais atenção ao cadastrar prazos, teto e documentação.

## Comportamento esperado

### Dados gerais

- Campos obrigatórios: **código** (único no sistema), **agência**, **título**, **data de abertura**, **data de encerramento** e **valor teto**.
- **Data de encerramento** não pode ser anterior à data de abertura; a mesma data em ambas é aceita.
- **Valor teto** deve ser maior que zero. É o único limite financeiro da chamada: o valor total previsto de cada proposta é validado contra ele.
- **Situação** (aberta / encerrada / cancelada): na criação é definida automaticamente como aberta se o encerramento é hoje ou no futuro, e como encerrada caso contrário; na edição pode ser alterada respeitando as regras da ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), inclusive o arquivamento das propostas não submetidas quando a chamada é encerrada ou cancelada.
- Código duplicado é recusado com mensagem no próprio campo, comparando sem diferenciar maiúsculas/minúsculas e ignorando espaços nas extremidades.

### Documentos obrigatórios

- Lista editável de documentos exigidos pela chamada, cada um com **nome** (obrigatório) e **observação** (opcional).
- Ações de adicionar, editar e remover item da lista; nomes repetidos na mesma chamada são recusados.
- A lista pode ficar vazia: nesse caso a chamada não exige documentos, e a submissão de suas propostas não é bloqueada por documentação.
- Um documento só é exigido porque a chamada o define: não há lista global de documentos no sistema.

### Edição de chamada com propostas vinculadas

- Ao salvar alterações em chamada que já possui propostas, o sistema exibe aviso com a quantidade de propostas afetadas e pede confirmação.
- Alterações em prazos, valor teto e documentos passam a valer para as propostas **não submetidas** — que podem, a partir daí, ficar impedidas de submeter.
- Propostas **submetidas** não são afetadas: seus dados e documentos já estão congelados e sua submissão continua válida.
- Antecipar a data de encerramento para uma data já passada encerra a chamada e arquiva suas propostas em rascunho ou em preparação com motivo `prazo perdido`; o aviso de confirmação informa quantas propostas serão arquivadas.
- Remover um documento obrigatório mantém os anexos já enviados pelas propostas não submetidas, mas eles deixam de ser exigidos e são apresentados como documentos extras na ficha da proposta.

### Estados e erros

- Formulário em branco na criação; carregado com os dados atuais na edição, com estado de carregamento e mensagem de erro caso a chamada não seja encontrada.
- Validações são exibidas por campo ao salvar; nenhum dado é gravado parcialmente.
- **Cancelar** descarta as alterações após confirmação, se houver mudanças pendentes.

## Navegação e integrações

- **De onde vem:** tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)), por **Nova chamada** ou **Editar**, e ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), por **Editar**.
- **Para onde vai:** ficha da chamada recém-criada ou editada após salvar, com mensagem de confirmação; retorna à tela inicial ao cancelar a criação.
- **É consumido por:** formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)) para o valor teto, e ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)) para a lista de documentos obrigatórios e o prazo de submissão.

## Critérios de aceite

- **Dado que** preencho código, agência, título, abertura, encerramento e valor teto válidos, **quando** salvo, **então** a chamada é criada como aberta e aparece na listagem inicial.
- **Dado que** informo encerramento anterior à abertura, **quando** tento salvar, **então** a gravação é recusada com mensagem no campo de encerramento.
- **Dado que** informo um código já existente, **quando** tento salvar, **então** a gravação é recusada com mensagem de código duplicado.
- **Dado que** informo valor teto igual a zero, **quando** tento salvar, **então** a gravação é recusada com mensagem no campo de valor teto.
- **Dado que** adiciono dois documentos obrigatórios, **quando** salvo, **então** as propostas dessa chamada passam a exigir esses dois documentos para submeter.
- **Dado que** a chamada possui propostas não submetidas, **quando** confirmo a redução do valor teto, **então** a alteração é gravada e as propostas cujo valor previsto excede o novo teto ficam impedidas de submeter.
- **Dado que** a chamada possui uma proposta em preparação, **quando** confirmo a alteração do encerramento para uma data passada, **então** a chamada passa a encerrada e a proposta é arquivada por prazo perdido.
- **Dado que** a chamada possui uma proposta submetida, **quando** altero seus documentos obrigatórios, **então** a proposta submetida permanece inalterada e válida.

## Observações e decisões

- A chamada tem um único limite financeiro, o valor teto: limites por rubrica saíram do escopo para manter o cadastro enxuto, e o orçamento da proposta passou a ser um valor total previsto ([PRD-00](./PRD-00-indice.md)).
- Documentos obrigatórios pertencem à chamada, e não a um catálogo global, exatamente como descrito no minimundo.
- Não há anexo do arquivo do edital no MVP; o título e o código são suficientes para localizar o documento original fora do sistema.
