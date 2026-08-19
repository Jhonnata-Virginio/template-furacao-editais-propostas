## Context

Ver `proposal.md` - Why. Este é o primeiro modelo de dados do projeto: ainda não existe nenhuma tabela no PostgreSQL (stack decidida em `docs/Constituicao/tech-stack.md`). As três entidades — Chamada, Proposta, Coordenador — nascem juntas porque Proposta depende das outras duas (chave estrangeira para Chamada e para Coordenador).

## Goals / Non-Goals

**Goals:**
- Definir o esquema relacional (tabelas, colunas, tipos e restrições) que satisfaz as regras descritas nas specs de `captacao/chamadas`, `captacao/coordenadores` e `captacao/propostas`.
- Deixar explícito como cada restrição de negócio (unicidade, coerência de datas, numeração sequencial, imutabilidade pós-submissão) se traduz em constraint de banco ou em regra de aplicação.

**Non-Goals:**
- Não define migrations, ORM ou biblioteca de acesso a dados — fica para a change de scaffolding do projeto Next.js.
- Não define autenticação/sessão do coordenador (hash de senha, login) além de exigir que a senha seja armazenada de forma protegida — o mecanismo de autenticação é uma capability futura.
- Não define o fluxo de decisão/aprovação da proposta pela chamada (aceite, parecer, recurso) — fora do escopo desta modelagem inicial.

## Decisões

**Três tabelas separadas (`chamadas`, `propostas`, `coordenadores`) em vez de uma tabela única ou de embutir coordenador dentro de proposta.**
Racional: as três entidades têm ciclos de vida e regras de unicidade próprios (código da chamada, e-mail do coordenador), e uma chamada ou coordenador se relaciona com várias propostas ao longo do tempo — uma modelagem normalizada evita duplicação e mantém a integridade referencial pelo próprio banco.
Alternativa rejeitada: desnormalizar coordenador dentro da proposta (copiar nome/e-mail a cada proposta) — simplificaria a leitura, mas quebraria a regra de que o histórico de propostas de um coordenador inativado precisa continuar íntegro e consistente com o cadastro único dele.

**Numeração sequencial anual da proposta implementada como sequência controlada por transação, não como `SERIAL`/`IDENTITY` global.**
Racional: o número precisa reiniciar (ou ser calculado) por ano e nunca pode repetir nem furar para uma proposta que nunca chegou a ser submetida (rascunhos descartados não devem consumir número). Uma sequência de banco por ano, ou uma tabela de controle (`ano`, `ultimo_numero`) incrementada dentro da mesma transação que submete a proposta, garante atomicidade sem depender de um `SERIAL` que avançaria mesmo em rascunhos.
Alternativa rejeitada: usar o `id` incremental da tabela `propostas` como número de submissão — rejeitada porque o `id` é atribuído no cadastro (rascunho), não na submissão, e propostas excluídas em rascunho deixariam buracos na numeração, violando a regra de número sequencial sem repetição atribuído somente na submissão.

**Número de submissão armazenado como duas colunas (`numero_sequencial`, `ano_submissao`), com o formato `XXX-YYYY` derivado na leitura, em vez de uma única coluna de texto pré-formatada.**
Racional: manter `numero_sequencial` como inteiro permite comparar, ordenar e gerar a próxima posição por cálculo aritmético simples (`MAX + 1` dentro da transação); a apresentação `XXX-YYYY` (zero-padding de 3 dígitos) é uma formatação de exibição, não uma propriedade de armazenamento. Isso também evita ambiguidade de parsing caso o número de dígitos precise crescer (ver Riscos).
Alternativa rejeitada: gravar diretamente a string formatada (`"001-2026"`) como identificador — rejeitada porque acopla a regra de exibição (zero-padding) ao dado armazenado, dificultando ordenação numérica e a evolução do formato se a contagem anual passar de 999.

**Constraints de banco para as regras verificáveis por linha; validação de aplicação para as regras que dependem de estado externo.**
Racional: regras como "encerramento >= abertura" (chamada), "e-mail único" (coordenador) e "código único" (chamada) são expressáveis como `CHECK`/`UNIQUE` no PostgreSQL e devem ficar no banco, que é a garantia mais forte contra corrida e bypass. Já regras que dependem de estado de outra entidade no momento da operação — coordenador inativo não pode assumir proposta pendente, alteração não pode tocar número de submissão de proposta já submetida, exclusão só em rascunho — exigem lógica de aplicação (ou trigger), porque envolvem decisão condicional entre tabelas/estados, não uma constraint simples de linha.
Alternativa rejeitada: implementar tudo via trigger de banco — rejeitada por concentrar regra de negócio fora da camada de aplicação (Next.js), dificultando teste e leitura; mantém-se trigger apenas onde a garantia de atomicidade é crítica (numeração sequencial).

## Esquema de dados (nível lógico)

**`chamadas`**
- `codigo` (único, obrigatório)
- `agencia` (obrigatório)
- `titulo` (obrigatório)
- `data_abertura` (obrigatório)
- `data_encerramento` (obrigatório; `CHECK data_encerramento >= data_abertura`)
- `valor_teto` (obrigatório)
- `limites_por_rubrica` (obrigatório; estrutura chave-valor ou tabela auxiliar, a detalhar na implementação)
- `status` (obrigatório; enum `aberta | encerrada | cancelada`; padrão `aberta`)

**`coordenadores`**
- `codigo_interno` (único, obrigatório)
- `nome_completo` (obrigatório)
- `email` (único, obrigatório; identifica o acesso)
- `senha_hash` (obrigatório; nunca armazenado em texto plano)
- `status_ativo` (obrigatório; booleano, padrão verdadeiro)
- `criado_em` (obrigatório; preenchido no cadastro)
- `alterado_em` (obrigatório; atualizado a cada alteração)

**`propostas`**
- `chamada_id` (obrigatório; referência a `chamadas`)
- `coordenador_id` (obrigatório; referência a `coordenadores`; só pode referenciar coordenador com `status_ativo = verdadeiro` no momento em que a proposta está pendente — regra de aplicação, ver Decisões)
- `titulo` (obrigatório)
- `resumo`
- `duracao`
- `responsavel`
- `observacoes_internas`
- `orcamento`
- `status` (obrigatório; enum `rascunho | em_preparacao | submetida`; padrão `rascunho`)
- `numero_sequencial` (inteiro; nulo até a submissão; representa o `XXX` do número de submissão, começando em 1 por ano; atribuído apenas na transição para `submetida`, nunca reatribuído nem alterado depois)
- `ano_submissao` (inteiro; nulo até a submissão; representa o `YYYY` do número de submissão; junto com `numero_sequencial` forma a chave de unicidade anual — `UNIQUE (ano_submissao, numero_sequencial)`)

O número de submissão exibido ao usuário (`XXX-YYYY`, ex.: `001-2026`) é a formatação de `numero_sequencial` com zero-padding de 3 dígitos, seguido de `-` e `ano_submissao` — calculada na leitura, não armazenada como string.

## Riscos / Trade-offs

- **Risco:** condição de corrida na atribuição do número sequencial anual se duas submissões ocorrerem simultaneamente. → **Mitigação:** atribuir o número dentro da mesma transação que muda o status para `submetida`, usando `SELECT ... FOR UPDATE` na linha de controle do ano (ou uma sequência dedicada por ano), garantindo serialização apenas no momento da submissão.
- **Risco:** regras que dependem de estado entre tabelas (coordenador inativo, imutabilidade pós-submissão, exclusão restrita a rascunho) ficam na aplicação, então um acesso direto ao banco fora da aplicação poderia violá-las. → **Mitigação:** aceitável nesta fase porque não há outro cliente de escrita além da aplicação Next.js; se isso mudar, promover essas regras para constraints/triggers de banco.
- **Risco:** `limites_por_rubrica` como estrutura livre (chave-valor) dificulta validação de soma/limite agregado. → **Mitigação:** deixado como dúvida em aberto (ver abaixo); resolver antes de implementar a tabela.
- **Risco:** o formato `XXX-YYYY` sugere 3 dígitos fixos; se um ano tiver mais de 999 propostas submetidas, a posição sequencial passaria a ter 4 dígitos. → **Mitigação:** `numero_sequencial` é armazenado como inteiro sem limite de tamanho; a formatação de exibição usa zero-padding mínimo de 3 dígitos (`printf`-style `%03d`), então o número simplesmente cresce para `1000-2026` sem quebrar unicidade ou ordenação — não é um teto rígido, só a largura mínima de exibição.

## Dúvidas em aberto

- **Formato de `limites_por_rubrica`**: modelar como tabela auxiliar (`chamada_rubricas`, uma linha por rubrica) ou como coluna estruturada (JSON) na própria `chamadas`? Não altera as specs (comportamento observável é o mesmo: a chamada tem limites por rubrica), mas altera o esquema físico — decidir na change de implementação do schema.
- **Formato de `resumo`, `duracao`, `responsavel`, `observacoes_internas`, `orcamento` da proposta**: tipos de dado (texto livre vs. estruturado, moeda vs. numérico) não foram especificados pelo usuário; assumir texto/numérico simples até haver PRD de tela que detalhe o formato de entrada.
