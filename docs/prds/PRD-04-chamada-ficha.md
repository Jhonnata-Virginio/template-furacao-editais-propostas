# PRD — Ficha da chamada

## Objetivo

Reunir em uma tela tudo o que define uma oportunidade externa — prazos, valor teto, documentos exigidos e situação — junto com **as propostas tentadas nela**, e permitir movimentar a situação da chamada (encerrar, cancelar, reabrir) ou iniciar uma nova proposta a partir dela.

## Usuário e contexto

Qualquer usuário autenticado. É a tela usada quando surge um novo edital e é preciso conferir prazo, teto e documentação antes de preparar uma proposta, e quando se quer saber o que já foi tentado naquela chamada e em que pé está cada tentativa.

## Comportamento esperado

### Cabeçalho e dados gerais

- Exibe **código**, **agência**, **título**, **situação**, **data de abertura**, **data de encerramento** (vencimento), **dias restantes** (quando a chamada está aberta) e **valor teto**.
- Indicação visual da situação e destaque de urgência quando o encerramento é em até 7 dias ou já passou.
- Datas de criação e de última alteração do registro, apenas leitura.
- Ações indisponíveis são exibidas desabilitadas com o motivo em texto curto, em vez de simplesmente ocultadas.

### Documentos exigidos

- Lista, somente leitura, dos documentos obrigatórios definidos pela chamada, cada um com **nome** e **observação**.
- Cada documento indica em quantas propostas não submetidas da chamada ele ainda está faltando, ligando a exigência ao trabalho pendente.
- Se a lista está vazia, o bloco informa que a chamada não exige documentos e que a submissão de suas propostas não é bloqueada por documentação.
- A lista é editada no formulário de chamada ([PRD-05](./PRD-05-chamada-formulario.md)), não aqui.

### Propostas vinculadas

- Tabela das propostas desta chamada, com **número** (ou traço, quando não submetida), **título**, **coordenador**, **tipo**, **estado**, **motivo do arquivamento** (quando arquivada), **resultado** (quando submetida), **valor total previsto** e **data de submissão**.
- Contadores por estado no topo do bloco: rascunho, em preparação, submetidas e arquivadas.
- Ordenação padrão pela data da última movimentação, mais recente primeiro; permite ordenar por número, título e estado.
- Cada linha indica, em texto curto, o que impede a submissão daquela proposta: documentos obrigatórios faltantes (quantidade), valor previsto acima do teto, coordenador inativo, chamada não aberta.
- Clique na linha abre a ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)).
- Ação **Ver todas na consulta** abre a consulta de propostas ([PRD-06](./PRD-06-propostas.md)) já filtrada por esta chamada.
- Estado vazio do bloco: mensagem de que nenhuma proposta foi registrada nesta chamada, com atalho para **Nova proposta nesta chamada** quando a chamada está aberta.

### Ações sobre a chamada

- **Nova proposta nesta chamada**: disponível apenas quando a situação é `aberta` e o encerramento não passou. Abre o formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)) com a chamada pré-selecionada.
- **Editar**: abre o formulário de chamada ([PRD-05](./PRD-05-chamada-formulario.md)).
- **Encerrar**: pede confirmação e explica o efeito — a chamada deixa de aceitar submissões e não pode mais ser escolhida em novas propostas. As propostas em `rascunho` ou `em preparação` são arquivadas com motivo `prazo perdido`.
- **Cancelar**: pede confirmação informando **quantas propostas não submetidas serão arquivadas**. Ao confirmar, essas propostas passam a `arquivada` com motivo `chamada cancelada`, com evento no histórico de cada uma. Propostas já submetidas permanecem inalteradas e sua submissão continua válida.
- **Reabrir**: permitida somente para chamada `encerrada` ou `cancelada` cuja data de encerramento ainda não passou. Ao reabrir, as propostas arquivadas por `chamada cancelada` ou `prazo perdido` **decorrentes dessa movimentação** são desarquivadas e voltam a `rascunho` ou `em preparação` conforme as regras de prontidão do [PRD-07](./PRD-07-proposta-formulario.md), com evento no histórico. Propostas arquivadas por `desistência` não voltam.
- Não existe exclusão de chamada: uma oportunidade registrada por engano é cancelada, preservando o histórico da captação.
- Toda movimentação pede confirmação, informa o efeito sobre as propostas vinculadas e, em caso de falha, mantém a situação anterior sem gravação parcial.

### Manutenção automática na leitura

- Chamada `aberta` cuja data de encerramento já passou é exibida e atualizada como `encerrada` na primeira leitura da ficha.
- As propostas em `rascunho` ou `em preparação` são então arquivadas com motivo `prazo perdido`, com registro no histórico de cada uma.

### Estados e erros

- Estado de carregamento ao abrir; mensagem de chamada não encontrada com atalho para a tela inicial.
- Erro de carga exibe mensagem e ação **Tentar novamente**.

## Navegação e integrações

- **De onde vem:** tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)), link da chamada na ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)) e retorno do formulário de chamada ([PRD-05](./PRD-05-chamada-formulario.md)).
- **Para onde vai:** formulário de chamada (edição), formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)) com a chamada pré-selecionada, ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)) e consulta de propostas ([PRD-06](./PRD-06-propostas.md)) filtrada pela chamada.
- **Depende de:** a chamada (dados, situação, documentos exigidos), suas propostas (estado, resultado, orçamento e documentos anexados) e usuários (coordenador).

## Critérios de aceite

- **Dado que** abro a ficha de uma chamada com quatro propostas, **quando** a tela carrega, **então** vejo as quatro propostas com estado, resultado e contadores por estado.
- **Dado que** a chamada não tem nenhuma proposta, **quando** abro a ficha, **então** vejo o estado vazio do bloco com o atalho para criar a primeira proposta.
- **Dado que** a chamada está aberta, **quando** aciono **Nova proposta nesta chamada**, **então** o formulário de proposta abre com essa chamada já selecionada.
- **Dado que** a chamada está encerrada, **quando** vejo as ações, **então** **Nova proposta nesta chamada** está desabilitada com o motivo indicado.
- **Dado que** a chamada tem duas propostas em preparação e uma submetida, **quando** confirmo o cancelamento, **então** as duas não submetidas passam a arquivadas com motivo "chamada cancelada", a submetida permanece inalterada e cada arquivamento consta no histórico.
- **Dado que** cancelei a chamada por engano e a data de encerramento ainda não passou, **quando** aciono **Reabrir**, **então** a chamada volta a aberta e as propostas arquivadas por chamada cancelada são desarquivadas com evento no histórico.
- **Dado que** uma proposta desta chamada foi arquivada por desistência, **quando** reabro a chamada, **então** essa proposta permanece arquivada.
- **Dado que** a chamada está aberta com encerramento anterior a hoje, **quando** abro a ficha, **então** ela é exibida como encerrada e suas propostas não submetidas são arquivadas por prazo perdido.
- **Dado que** uma proposta da chamada tem dois documentos obrigatórios sem anexo, **quando** vejo sua linha, **então** o impedimento indica que faltam dois documentos obrigatórios.
- **Dado que** aciono **Ver todas na consulta**, **quando** a navegação conclui, **então** a consulta de propostas abre filtrada por esta chamada.

## Observações e decisões

- A ficha da chamada é onde o relacionamento chamada–proposta fica explícito: a chamada define as regras (prazo, teto, documentos) e concentra as tentativas feitas nela, conforme o [PRD-00](./PRD-00-indice.md).
- Encerrar e cancelar têm o mesmo efeito prático sobre as propostas não submetidas (arquivamento), mas motivos diferentes: `prazo perdido` registra que o prazo passou; `chamada cancelada` registra que a oportunidade deixou de existir. A distinção é a memória da captação.
- O desarquivamento na reabertura existe para não punir um cancelamento equivocado; a desistência, por ser decisão da equipe, continua irreversível.
- Não há anexo do arquivo do edital no MVP: código e título são suficientes para localizar o documento original fora do sistema.
