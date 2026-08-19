# PRD — Chamadas (tela inicial)

## Objetivo

Mostrar, ao entrar no sistema, as chamadas em acompanhamento — prazo, valor teto e propostas vinculadas —, com filtros por datas e situação, para que a equipe decida onde agir primeiro partindo sempre do prazo que vence antes.

## Usuário e contexto

Qualquer usuário autenticado. É a tela inicial após o login e o ponto de partida da rotina diária de captação: substitui a checagem manual de planilhas para saber quais oportunidades estão abertas, quanto tempo resta em cada uma e o que já foi tentado nelas.

## Comportamento esperado

### Listagem

- Tabela com **código**, **agência**, **título**, **abertura**, **encerramento** (vencimento), **dias restantes**, **valor teto**, **situação** e **propostas vinculadas**.
- A coluna **propostas vinculadas** mostra o total e a composição por estado, em texto curto (ex.: `4 (1 rascunho · 1 em preparação · 2 submetidas)`), tornando visível o vínculo entre a chamada e as tentativas feitas nela.
- Ordenação padrão pela **data de encerramento crescente**: o vencimento mais próximo primeiro. Chamadas com prazo já encerrado aparecem ao final. Permite ordenar também por abertura, código, agência e quantidade de propostas.
- Indicação visual da situação e da urgência pelos dias restantes: prazo encerrado, encerrando em até 7 dias, e demais casos.
- Contadores no topo, apenas leitura, calculados sobre o filtro aplicado: chamadas listadas, chamadas com encerramento em até 7 dias e propostas em preparação nessas chamadas.

### Filtros e busca

- Filtro por **situação**: `aberta`, `encerrada`, `cancelada` ou `todas`. **Por padrão, apenas `aberta`** — a tela abre focada nas oportunidades ainda disponíveis.
- Filtro por **datas**, com intervalo **de / até** aplicado sobre **encerramento** (padrão) ou sobre **abertura**, à escolha do usuário. Qualquer um dos dois limites do intervalo pode ficar em branco.
- Atalhos de período para o uso mais comum: `encerra nos próximos 7 dias`, `encerra nos próximos 30 dias` e `prazo encerrado`.
- Busca em campo único por código, agência ou título, sem diferenciar maiúsculas/minúsculas e por correspondência parcial.
- Filtros e busca são combináveis; a tela indica quais estão aplicados e oferece **Limpar filtros**, que restaura o padrão (situação `aberta`, sem intervalo de datas).

### Ações

- Clique na linha abre a **ficha da chamada** ([PRD-04](./PRD-04-chamada-ficha.md)), onde estão as propostas daquela chamada.
- Ações por linha: **Nova proposta nesta chamada** (apenas quando a situação é `aberta`), **Ver propostas** (abre a consulta de propostas já filtrada por esta chamada) e **Editar** ([PRD-05](./PRD-05-chamada-formulario.md)).
- Ação de topo: **Nova chamada**.
- A tela não altera chamadas nem propostas além da manutenção automática descrita abaixo: encerrar, cancelar e reabrir acontecem na ficha da chamada.

### Manutenção automática na leitura

- Chamada com situação `aberta` cuja data de encerramento já passou é atualizada para `encerrada` na primeira leitura da tela.
- As propostas em `rascunho` ou `em preparação` dessas chamadas são **arquivadas com motivo `prazo perdido`**, com registro no histórico de cada uma, conforme decisão do [PRD-00](./PRD-00-indice.md). Isso se reflete de imediato na coluna de propostas vinculadas.
- Não há processo agendado no MVP: a marcação acontece na leitura.

### Estados e navegação de apoio

- Estado vazio: mensagem coerente com o filtro ("nenhuma chamada aberta no momento" ou "nenhuma chamada encontrada para estes filtros"), com atalhos para **Nova chamada** e **Limpar filtros**.
- Estado de carregamento com indicador; erro de carga exibe mensagem e ação **Tentar novamente**.
- Cabeçalho com navegação para Propostas e Usuários, retorno a esta tela pelo logo do sistema, e ação de encerrar sessão.

## Navegação e integrações

- **De onde vem:** login ([PRD-01](./PRD-01-login.md)), logo do sistema no cabeçalho ou rota `/chamadas`, que redireciona para `/`.
- **Para onde vai:** ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)) ao clicar na linha; formulário de chamada ([PRD-05](./PRD-05-chamada-formulario.md)) por **Nova chamada** e **Editar**; formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)) com a chamada pré-selecionada; consulta de propostas ([PRD-06](./PRD-06-propostas.md)) filtrada pela chamada.
- **Depende de:** chamadas (situação, datas e valor teto) e propostas vinculadas, para os contadores por estado.

## Critérios de aceite

- **Dado que** existem chamadas abertas, encerradas e canceladas, **quando** abro a tela inicial, **então** vejo apenas as abertas, ordenadas do encerramento mais próximo para o mais distante.
- **Dado que** quero rever oportunidades passadas, **quando** troco o filtro de situação para "todas", **então** chamadas encerradas e canceladas passam a ser listadas.
- **Dado que** informo o intervalo de encerramento entre hoje e o fim do mês, **quando** aplico o filtro, **então** apenas chamadas que encerram nesse intervalo são listadas.
- **Dado que** apliquei filtros de data e busca, **quando** aciono **Limpar filtros**, **então** a tela volta ao padrão de chamadas abertas ordenadas pelo vencimento mais próximo.
- **Dado que** uma chamada aberta encerra em três dias, **quando** abro a tela, **então** ela aparece com destaque de urgência e a indicação de dias restantes.
- **Dado que** uma chamada possui quatro propostas em estados diferentes, **quando** vejo sua linha, **então** a coluna de propostas vinculadas mostra o total e a composição por estado.
- **Dado que** uma chamada está encerrada, **quando** vejo sua linha, **então** a ação **Nova proposta nesta chamada** não está disponível.
- **Dado que** uma chamada aberta tem data de encerramento anterior a hoje e uma proposta em preparação, **quando** abro a tela, **então** a chamada passa a encerrada, a proposta é arquivada por prazo perdido e o evento consta no histórico dela.
- **Dado que** clico na linha de uma chamada, **quando** a navegação conclui, **então** vejo a ficha dessa chamada com as propostas vinculadas.
- **Dado que** nenhuma chamada atende aos filtros aplicados, **quando** a busca conclui, **então** vejo o estado vazio com a ação de limpar filtros.

## Observações e decisões

- A tela inicial é a listagem de chamadas porque a oportunidade é a unidade de acompanhamento do laboratório: a proposta só existe dentro de uma chamada. A urgência de trabalho fica visível aqui pelos dias restantes e pela contagem de propostas em preparação de cada chamada, sem precisar de uma tela separada de pendências.
- O padrão "situação aberta + vencimento mais próximo" é a decisão de abertura da tela registrada no [PRD-00](./PRD-00-indice.md): é o recorte que responde "o que precisa sair agora".
- O encerramento automático por data e o arquivamento por prazo perdido são aplicados na leitura da tela, sem processo agendado, coerente com a decisão do [PRD-00](./PRD-00-indice.md).
- A listagem não permite movimentar chamadas nem propostas: as regras de situação ficam concentradas na ficha da chamada e na ficha da proposta.
