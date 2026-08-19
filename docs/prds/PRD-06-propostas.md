# PRD — Consulta de propostas

## Objetivo

Permitir localizar qualquer tentativa de captação — enviada, em andamento ou arquivada — buscando por proposta, chamada, coordenador ou título, e chegar à ficha correspondente.

## Usuário e contexto

Qualquer usuário autenticado. É o caminho alternativo à navegação por chamadas: serve para responder perguntas do dia a dia do laboratório quando o ponto de partida é a proposta, e não a oportunidade — o que este coordenador enviou, qual foi o resultado de uma proposta antiga, o que já foi arquivado e por quê.

## Comportamento esperado

- Tabela com **número** da proposta, **título**, **chamada** (código e agência), **coordenador**, **tipo**, **estado**, **motivo do arquivamento** (quando arquivada), **resultado** (quando submetida), **valor total previsto** e **data de submissão**.
- Proposta sem número (não submetida) exibe um traço na coluna de número, indicando que ainda não recebeu numeração.
- A coluna **chamada** é um link para a ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), mantendo visível o vínculo de cada proposta com sua oportunidade.
- Busca em campo único que consulta, simultaneamente: número da proposta, título da proposta, código e título da chamada e nome do coordenador. Sem diferenciar maiúsculas/minúsculas e por correspondência parcial.
- Filtros combináveis: **estado** (rascunho, em preparação, submetida, arquivada), **motivo do arquivamento** (prazo perdido, chamada cancelada, desistência — disponível quando o estado arquivada está selecionado), **resultado** (pendente, aprovado, reprovado, suplente), **chamada** e **coordenador**. Sem filtro, exibe todas as propostas.
- Ordenação padrão pela data da última movimentação, mais recente primeiro; permite ordenar por número, título e data de submissão.
- Ao abrir a tela, o sistema arquiva com motivo `prazo perdido` as propostas em rascunho ou em preparação cuja chamada já encerrou, registrando o evento no histórico de cada uma.
- Ações: **Nova proposta**; clique na linha abre a ficha; **Editar** disponível apenas para propostas em rascunho ou em preparação.
- Paginação simples quando o resultado excede uma página, com indicação da quantidade total de propostas encontradas.
- Estados de carregamento, vazio ("nenhuma proposta encontrada para esta busca", com ação de limpar filtros) e erro com **Tentar novamente**.

## Navegação e integrações

- **De onde vem:** cabeçalho das telas internas; ação **Ver propostas** da tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)) e **Ver todas na consulta** da ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), ambas abrindo a tela já filtrada por chamada.
- **Para onde vai:** ficha da proposta ([PRD-08](./PRD-08-proposta-ficha.md)), formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)) e ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)) pelo link da chamada.
- **Depende de:** propostas, chamadas vinculadas e usuários (coordenador).

## Critérios de aceite

- **Dado que** busco pelo nome de um coordenador, **quando** confirmo a busca, **então** vejo todas as propostas em que ele é coordenador, independentemente do estado.
- **Dado que** busco pelo código de uma chamada, **quando** confirmo a busca, **então** vejo as propostas vinculadas àquela chamada.
- **Dado que** busco pelo número `2026/0007`, **quando** confirmo a busca, **então** vejo a proposta correspondente.
- **Dado que** filtro por estado "submetida" e resultado "aprovado", **quando** aplico os filtros, **então** apenas propostas submetidas e aprovadas são listadas.
- **Dado que** filtro por estado "arquivada" e motivo "chamada cancelada", **quando** aplico os filtros, **então** apenas propostas arquivadas por cancelamento da chamada são listadas.
- **Dado que** uma proposta foi submetida e depois reprovada, **quando** vejo sua linha, **então** ela continua exibindo o número recebido na submissão.
- **Dado que** uma proposta está em rascunho, **quando** vejo sua linha, **então** a coluna de número exibe um traço e a ação **Editar** está disponível.
- **Dado que** uma proposta está submetida, **quando** vejo sua linha, **então** a ação **Editar** não está disponível e o acesso é feito pela ficha.
- **Dado que** uma proposta está arquivada, **quando** vejo sua linha, **então** o motivo do arquivamento é exibido e a ação **Editar** não está disponível.
- **Dado que** clico no código da chamada de uma linha, **quando** a navegação conclui, **então** vejo a ficha daquela chamada com todas as propostas dela.
- **Dado que** nenhuma proposta atende aos filtros aplicados, **quando** a busca conclui, **então** vejo o estado vazio com a ação de limpar filtros.

## Observações e decisões

- Esta tela é a busca transversal por propostas; o acompanhamento por oportunidade acontece na tela inicial de chamadas ([PRD-02](./PRD-02-chamadas.md)) e na ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), conforme o relacionamento definido no [PRD-00](./PRD-00-indice.md).
- Exportação, agrupamentos e indicadores agregados ficam fora do escopo: o minimundo exclui relatórios do MVP.
- A tela não altera propostas além do arquivamento automático por prazo perdido; toda movimentação acontece na ficha, para concentrar as regras de estado em um único lugar.
