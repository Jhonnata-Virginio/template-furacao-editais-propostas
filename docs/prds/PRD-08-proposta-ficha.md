# PRD — Ficha da proposta

## Objetivo

Concentrar em uma tela tudo o que se sabe sobre uma tentativa de captação — dados gerais, chamada vinculada, tipo, documentos, orçamento e histórico — e permitir movimentá-la: anexar documentos, submeter, registrar resultado ou arquivar por desistência.

## Usuário e contexto

Qualquer usuário autenticado. É a tela onde as regras de movimentação são aplicadas e onde se responde "em que pé está esta proposta?", tanto durante a preparação quanto anos depois, ao consultar o que foi enviado e decidido.

## Comportamento esperado

### Cabeçalho

- Exibe **título**, **número** (ou "sem número — não submetida"), **estado**, **motivo do arquivamento** (quando arquivada), **resultado** (quando submetida) e **chamada** com código, agência e data de encerramento.
- O código da chamada é um link para a ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)): a proposta sempre se lê no contexto da oportunidade que a originou.
- Indicação visual do estado e, quando a proposta está em rascunho ou em preparação, dos dias restantes até o encerramento da chamada.
- Ações disponíveis conforme o estado, descritas abaixo. Ações indisponíveis são exibidas desabilitadas com o motivo em texto curto, em vez de simplesmente ocultadas.

### Dados gerais e tipo

- Bloco de dados gerais: chamada, coordenador, responsável interno, título, resumo, duração em meses, observações internas, data de criação e data da última alteração.
- Bloco do tipo: tipo escolhido e seus campos extras, apenas descritivos.
- Coordenador inativo é sinalizado no bloco de dados gerais, com aviso de que a submissão está bloqueada até a troca.
- Ação **Editar** abre o formulário ([PRD-07](./PRD-07-proposta-formulario.md)); disponível somente para proposta em rascunho ou em preparação.
- Em proposta submetida ou arquivada, o bloco é somente leitura, exceto **observações internas**, que permanecem editáveis diretamente na ficha e registram evento no histórico.

### Documentos

- Lista os documentos obrigatórios definidos pela chamada, cada um com nome, observação, situação (anexado / faltando) e, quando anexado, nome do arquivo, autor e data do anexo.
- Ações por documento, apenas para proposta em rascunho ou em preparação: **Anexar**, **Substituir** e **Remover anexo**. Remover anexo pede confirmação.
- Documentos anexados que deixaram de ser exigidos pela chamada aparecem em uma seção **documentos extras**, sem bloquear nada.
- Se a chamada não exige documentos, o bloco informa que não há documentação obrigatória.
- Em proposta submetida ou arquivada, os anexos ficam somente leitura e disponíveis para download, junto ao comprovante de submissão quando houver.

### Orçamento

- Exibe o **valor total previsto** da proposta, o **valor teto da chamada** e a situação (dentro do teto / acima do teto), com a diferença entre os dois valores.
- Valor previsto acima do teto é destacado como impedimento à submissão.

### Movimentação

- **Submeter** (disponível em preparação). O sistema verifica todas as condições antes de aceitar:
  - a chamada está **aberta** e sua data de encerramento não passou;
  - o coordenador está **ativo**;
  - todos os documentos obrigatórios da chamada estão anexados;
  - o valor total previsto está dentro do valor teto da chamada.
- Quando alguma condição falha, a submissão é recusada e a tela lista **todos** os impedimentos encontrados, sem alterar o estado da proposta.
- Quando todas as condições são atendidas, o usuário confirma a submissão informando **data da submissão** (padrão: hoje) e o **comprovante** (identificador de protocolo da agência e, opcionalmente, arquivo). Ao confirmar:
  - a proposta recebe o **número sequencial anual** no formato `AAAA/NNNN`, único e nunca reutilizado — o número é gerado **no ato da submissão**, independentemente de qualquer resultado futuro;
  - o estado passa a **submetida** e o resultado a **pendente**;
  - o conteúdo é congelado (dados gerais, tipo, orçamento e documentos tornam-se somente leitura);
  - o comprovante é registrado e exibido na ficha;
  - o evento é registrado no histórico com autor e data/hora.
- **Registrar resultado** (disponível em submetida): registro **manual**, feito por um usuário do sistema, escolhendo entre **aprovado**, **reprovado** ou **suplente**, com data do resultado e observação opcional. O resultado pode ser corrigido depois, também manualmente, e cada alteração gera um evento no histórico com autor e data/hora. O sistema nunca altera o resultado por conta própria: não há integração, importação ou consulta a API da agência no MVP.
- Alterar o resultado não muda o estado nem o número da proposta: uma proposta reprovada continua **submetida** e mantém o número recebido na submissão.
- **Arquivar por desistência** (disponível em rascunho, em preparação e submetida): exige **motivo** em texto obrigatório e não vazio; a proposta passa a **arquivada** com motivo `desistência`, mantém número, comprovante e resultado quando já submetida, e não volta a ser editável.
- **Arquivamento automático** pelo sistema, sem ação do usuário, para proposta em rascunho ou em preparação:
  - motivo `prazo perdido`, quando a data de encerramento da chamada já passou. Ocorre na primeira leitura da proposta (tela inicial, ficha da chamada, consulta ou esta ficha), registra o evento no histórico e não é reversível pela interface.
  - motivo `chamada cancelada`, quando a chamada vinculada é cancelada, conforme o [PRD-04](./PRD-04-chamada-ficha.md). É desfeito apenas se a chamada for reaberta, caso em que a proposta é desarquivada e volta a rascunho ou em preparação pelas regras de prontidão, com evento no histórico.
- Proposta arquivada não aceita submissão nem novas movimentações — apenas edição de observações internas — e permanece consultável.
- Toda ação de movimentação pede confirmação e informa o efeito irreversível quando houver.

### Histórico

- Linha do tempo em ordem decrescente, somente leitura, com data/hora, autor e descrição do evento: criação, entrada em preparação, retorno a rascunho, alteração de observações internas, anexo e remoção de documento, submissão (com número gerado), registro e correção de resultado, arquivamento (com motivo e, na desistência, o texto informado) e desarquivamento.
- Eventos de arquivamento automático são registrados com a indicação de que foram aplicados pelo sistema; os demais, com o usuário autor.
- Autor inativo continua identificado no histórico.
- O histórico não é auditoria campo a campo: registra os eventos da tentativa, conforme escopo do MVP.

### Estados e erros

- Estado de carregamento ao abrir; mensagem de proposta não encontrada com atalho para a consulta.
- Falha em qualquer movimentação mantém o estado anterior e exibe o motivo, sem gravação parcial.

## Navegação e integrações

- **De onde vem:** ficha da chamada ([PRD-04](./PRD-04-chamada-ficha.md)), consulta de propostas ([PRD-06](./PRD-06-propostas.md)) e retorno do formulário de proposta ([PRD-07](./PRD-07-proposta-formulario.md)).
- **Para onde vai:** formulário de proposta (edição), ficha da chamada pelo link da chamada, e consulta de propostas ao voltar.
- **Depende de:** chamada vinculada (situação, prazo, valor teto e documentos obrigatórios) e usuários (coordenador, responsável e autoria dos eventos).

## Critérios de aceite

- **Dado que** a proposta está em preparação com chamada aberta, coordenador ativo, todos os documentos anexados e valor previsto dentro do teto, **quando** confirmo a submissão informando data e comprovante, **então** ela recebe número no formato `AAAA/NNNN`, passa a submetida com resultado pendente, tem o conteúdo congelado e o evento registrado no histórico.
- **Dado que** falta um documento obrigatório e o valor previsto excede o teto, **quando** aciono **Submeter**, **então** a submissão é recusada listando os dois impedimentos e a proposta permanece em preparação sem número.
- **Dado que** duas propostas são submetidas no mesmo ano, **quando** ambas concluem a submissão, **então** recebem números sequenciais distintos do mesmo ano, sem reutilização de números de propostas anteriores.
- **Dado que** uma proposta foi submetida e depois reprovada, **quando** abro a ficha, **então** ela continua no estado submetida, exibindo o número recebido na submissão e o resultado reprovado.
- **Dado que** o coordenador da proposta foi inativado, **quando** abro a ficha, **então** vejo o aviso de coordenador inativo e a submissão está bloqueada.
- **Dado que** a proposta está submetida, **quando** abro a ficha, **então** os dados gerais, tipo, orçamento e documentos estão somente leitura e apenas observações internas, resultado, comprovante e desistência aceitam alteração.
- **Dado que** a proposta está submetida com resultado pendente, **quando** registro manualmente o resultado como suplente, **então** a ficha passa a exibir suplente e o histórico registra o evento com autor e data.
- **Dado que** registrei o resultado como reprovado por engano, **quando** corrijo manualmente para aprovado, **então** a ficha exibe aprovado e o histórico mantém os dois eventos.
- **Dado que** aciono **Arquivar por desistência** sem informar o motivo, **quando** tento confirmar, **então** a ação é recusada com mensagem de motivo obrigatório.
- **Dado que** informo o motivo e confirmo a desistência de uma proposta em preparação, **então** ela passa a arquivada com motivo desistência, não pode mais ser submetida e o texto do motivo consta no histórico.
- **Dado que** a proposta está em preparação e a chamada encerrou ontem, **quando** abro a ficha, **então** ela é arquivada com motivo prazo perdido, o evento é registrado e as ações de movimentação ficam indisponíveis.
- **Dado que** a chamada da proposta foi cancelada, **quando** abro a ficha, **então** a proposta aparece arquivada com motivo chamada cancelada e a submissão não está disponível.
- **Dado que** a chamada foi reaberta dentro do prazo, **quando** abro a ficha da proposta arquivada por chamada cancelada, **então** ela voltou a em preparação e o desarquivamento consta no histórico.
- **Dado que** a proposta nunca foi submetida, **quando** abro a ficha, **então** o número aparece como "sem número — não submetida".

## Observações e decisões

- Não há reabertura de proposta submetida: para manter a memória da tentativa, correções de rumo são registradas como resultado, arquivamento por desistência ou nova proposta na chamada seguinte.
- O arquivamento é o único estado terminal da proposta, e o **motivo** preserva a distinção que antes era feita por estados separados: `prazo perdido`, `chamada cancelada` e `desistência` ([PRD-00](./PRD-00-indice.md)).
- O arquivamento por prazo perdido não é reversível pela interface porque representa um fato externo (o prazo do edital passou); o arquivamento por cancelamento é reversível apenas pela reabertura da chamada, porque decorre de uma decisão registrada no sistema.
- A numeração é gerada na submissão, e não na criação nem na aprovação: marca o envio efetivo, evitando lacunas na sequência anual causadas por rascunhos abandonados e mantendo o número de propostas enviadas que não foram aprovadas.
- O resultado é sempre entrada manual: o comprovante é um registro simples (protocolo e arquivo opcional) e não há integração com a agência para validá-lo ou para buscar a decisão, conforme escopo do MVP.
