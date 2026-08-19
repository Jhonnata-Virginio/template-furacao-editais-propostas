# PRD — Formulário de proposta

## Objetivo

Cadastrar e corrigir uma proposta: vincular a chamada e o coordenador, descrever a tentativa, escolher o tipo e informar o orçamento previsto, deixando-a pronta para avançar até a submissão.

## Usuário e contexto

Qualquer usuário autenticado, ao registrar uma nova tentativa de captação em uma chamada ou corrigir dados de uma proposta ainda não submetida. É a tela em que o trabalho de preparação acontece, normalmente em várias sessões — daí a existência do rascunho.

## Comportamento esperado

### Dados obrigatórios e opcionais

- Obrigatórios para gravar: **chamada** e **título**.
- Demais campos, opcionais para gravar e usados na avaliação de prontidão: **coordenador**, **tipo**, **resumo**, **duração em meses**, **responsável interno**, **observações internas** e **valor total previsto**.
- **Chamada:** seleção com busca por código, agência ou título. Apenas chamadas **abertas** podem ser escolhidas. Quando a tela é aberta a partir da tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)) ou da ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), vem pré-selecionada.
- **Coordenador:** seleção entre usuários **ativos**. Usuários inativos não aparecem na lista.
- **Responsável interno:** seleção entre usuários ativos; por padrão, o usuário que criou a proposta.
- **Duração em meses:** número inteiro maior que zero, quando informada.
- **Observações internas:** texto livre, visível apenas dentro do sistema.

### Tipo e campos extras

- **Tipo** é escolhido em lista fixa de três opções, e cada uma exibe seus campos extras, todos descritivos e opcionais:
  - **Projeto de pesquisa:** linha de pesquisa, resultados esperados.
  - **Apoio a bolsas:** modalidade da bolsa, quantidade de bolsas, duração das bolsas em meses.
  - **Apoio a evento e infraestrutura:** natureza (evento ou infraestrutura), descrição do item ou evento, local e período.
- A troca de tipo é permitida somente enquanto a proposta não está submetida. Ao trocar, o sistema pede confirmação e substitui o tipo anterior, descartando os campos extras preenchidos no tipo abandonado.
- Os campos extras não geram regras de negócio nem validações próprias além de formato: servem apenas para descrever a proposta.

### Orçamento

- Campo único **valor total previsto**, opcional para gravar e não negativo.
- O formulário exibe, ao lado do campo, o **valor teto da chamada** vinculada e a diferença em relação ao valor informado.
- Valor previsto acima do valor teto é sinalizado como **aviso** no formulário: a gravação é permitida (para não travar o rascunho), mas a submissão será bloqueada até o ajuste.
- Trocar a chamada recalcula o teto exibido e mantém o valor já informado.

### Gravação e estados da proposta

- **Salvar** grava a proposta e mantém o usuário na ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)).
- Proposta criada nasce em **rascunho**, sem número.
- Ao salvar, se a proposta está em rascunho e possui chamada, coordenador **ativo** e título definidos, ela avança automaticamente para **em preparação**; a promoção é informada na mensagem de confirmação e registrada no histórico.
- Enquanto faltar algum desses três dados, a proposta permanece em rascunho e o formulário mostra o que falta para entrar em preparação.
- A alteração de uma proposta em preparação segue exatamente as mesmas regras do cadastro; se um dado obrigatório para a prontidão for removido (por exemplo, o coordenador), a proposta retorna a rascunho e o retorno é registrado no histórico.
- Alteração nunca gera nem altera número de proposta: o número existe apenas a partir da submissão.

### Propostas submetidas e arquivadas

- Proposta **submetida** não é editável nesta tela: o acesso direto à rota de edição redireciona para a ficha com aviso de conteúdo congelado. Observações internas, resultado, comprovante e arquivamento por desistência são tratados na ficha.
- Proposta **arquivada** — por `prazo perdido`, `chamada cancelada` ou `desistência` — também não é editável, preservando o registro da tentativa. O acesso à rota de edição redireciona para a ficha com o motivo do arquivamento.
- Se a chamada de uma proposta em rascunho ou em preparação for cancelada ou encerrada enquanto o formulário está aberto, a gravação é recusada com a explicação, e a proposta é arquivada pelas regras do [PRD-04](./PRD-04-chamada-ficha.md).

### Exclusão

- **Excluir** está disponível somente para proposta em **rascunho**, exige confirmação explícita com o título da proposta na mensagem e é irreversível.
- Proposta em preparação, submetida ou arquivada não pode ser excluída; nesses casos a ação não é exibida e a orientação é usar o arquivamento por desistência com motivo.
- Após excluir, o usuário retorna à ficha da chamada vinculada, com mensagem de confirmação.

### Estados e erros

- Formulário em branco na criação; carregado com os dados atuais na edição, com estado de carregamento e mensagem caso a proposta não exista.
- Validações exibidas por campo ao salvar; nada é gravado parcialmente.
- **Cancelar** descarta as alterações após confirmação, se houver mudanças pendentes.

## Navegação e integrações

- **De onde vem:** tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)) e ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)) com chamada pré-selecionada, consulta de propostas ([PRD-06](./PRD-06-propostas.md)) e ação **Editar** da ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)).
- **Para onde vai:** ficha da proposta após salvar; ficha da chamada após excluir.
- **Depende de:** chamadas abertas e seu valor teto ([PRD-05](./PRD-05-chamada-formulario.md)) e usuários ativos ([PRD-03](./PRD-03-usuarios.md)).

## Critérios de aceite

- **Dado que** informo apenas chamada e título, **quando** salvo, **então** a proposta é criada em rascunho, sem número, e o formulário indica que falta o coordenador para entrar em preparação.
- **Dado que** uma proposta em rascunho tem chamada e título, **quando** informo um coordenador ativo e salvo, **então** ela passa a em preparação e o evento consta no histórico.
- **Dado que** estou selecionando o coordenador, **quando** abro a lista, **então** usuários inativos não são oferecidos.
- **Dado que** estou selecionando a chamada, **quando** abro a lista, **então** apenas chamadas abertas são oferecidas.
- **Dado que** deixo o título em branco, **quando** tento salvar, **então** a gravação é recusada com mensagem no campo de título.
- **Dado que** a proposta é do tipo apoio a bolsas com campos extras preenchidos, **quando** troco para projeto de pesquisa e confirmo, **então** o tipo é substituído e os campos extras anteriores são descartados.
- **Dado que** o valor total previsto excede o valor teto da chamada, **quando** salvo, **então** a proposta é gravada com aviso de estouro e a ficha indica que a submissão está bloqueada.
- **Dado que** uma proposta em preparação tem coordenador definido, **quando** removo o coordenador e salvo, **então** ela retorna a rascunho e o evento consta no histórico.
- **Dado que** uma proposta está submetida, **quando** acesso sua rota de edição, **então** sou redirecionado para a ficha com aviso de que o conteúdo está congelado.
- **Dado que** uma proposta foi arquivada por prazo perdido, **quando** acesso sua rota de edição, **então** sou redirecionado para a ficha com o motivo do arquivamento.
- **Dado que** uma proposta está em rascunho, **quando** confirmo a exclusão, **então** ela é removida e retorno à ficha da chamada.
- **Dado que** uma proposta está em preparação, **quando** abro suas ações, **então** a exclusão não está disponível.

## Observações e decisões

- O avanço para **em preparação** é automático ao atender às três condições do minimundo (chamada, coordenador ativo e título), em vez de exigir um botão: evita que uma proposta pronta fique parada por esquecimento.
- Estouro de orçamento é aviso na gravação e bloqueio na submissão: o rascunho precisa aceitar valores em construção, mas o envio não pode desrespeitar o edital.
- O orçamento é um valor total previsto comparado ao valor teto da chamada: a distribuição por rubricas saiu do escopo do MVP ([PRD-00](./PRD-00-indice.md)).
- Anexo de documentos não acontece aqui, e sim na ficha, onde a lista de documentos obrigatórios da chamada é apresentada junto ao estado da submissão.
