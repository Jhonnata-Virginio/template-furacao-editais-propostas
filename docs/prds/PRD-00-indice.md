# PRD-00 — Índice

## Projeto

- **Nome:** Acompanhamento de Editais e Propostas — Laboratório IDE.IA
- **Objetivo:** substituir planilhas e mensagens por um cadastro simples de chamadas públicas e propostas, garantindo que documentos obrigatórios, prazos e decisões sejam registrados sem perder a memória de cada tentativa de captação.

## Telas

| Tela | Rota | PRD | Status |
| --- | --- | --- | --- |
| Acesso ao sistema | `/login` | [PRD-01-login.md](./PRD-01-login.md) | planejada |
| Chamadas (tela inicial) | `/` | [PRD-02-chamadas.md](./PRD-02-chamadas.md) | planejada |
| Usuários | `/usuarios` | [PRD-03-usuarios.md](./PRD-03-usuarios.md) | planejada |
| Ficha da chamada | `/chamadas/:codigo` | [PRD-04-chamada-ficha.md](./PRD-04-chamada-ficha.md) | planejada |
| Formulário de chamada | `/chamadas/nova`, `/chamadas/:codigo/editar` | [PRD-05-chamada-formulario.md](./PRD-05-chamada-formulario.md) | planejada |
| Consulta de propostas | `/propostas` | [PRD-06-propostas.md](./PRD-06-propostas.md) | planejada |
| Formulário de proposta | `/propostas/nova`, `/propostas/:id/editar` | [PRD-07-proposta-formulario.md](./PRD-07-proposta-formulario.md) | planejada |
| Ficha da proposta | `/propostas/:id` | [PRD-08-proposta-ficha.md](./PRD-08-proposta-ficha.md) | planejada |

`/chamadas` redireciona para `/`: a listagem de chamadas é a tela inicial do sistema.

## Fluxo ou dependências

```text
Login ──> Chamadas (tela inicial, /) ──> Ficha da chamada ──> Propostas da chamada ──> Ficha da proposta
              │                               │                                            │
              │                               ├──> Formulário de chamada                    │
              │                               └──> Formulário de proposta ───────────────────┘
              │                                            (chamada pré-selecionada)
              ├──> Consulta de propostas ──> Ficha da proposta
              └──> Usuários
```

### Relacionamento entre chamada e proposta

```text
Chamada (1) ──────────< Proposta (0..N)
   │                        │
   │ define                 │ herda e é validada por
   ├─ prazo (encerramento)  ├─ prazo de submissão
   ├─ valor teto            ├─ limite do valor previsto
   └─ documentos exigidos   └─ documentos a anexar

situação da chamada ──> ciclo da proposta
   aberta      ──> permite criar, preparar e submeter
   encerrada   ──> propostas não submetidas são arquivadas (prazo perdido)
   cancelada   ──> propostas não submetidas são arquivadas (chamada cancelada)
```

- **Cardinalidade:** uma chamada tem zero ou muitas propostas; uma proposta pertence a exatamente **uma** chamada, obrigatória desde a criação.
- **Ponto de partida:** a chamada é a entrada do sistema. A tela inicial lista chamadas e a ficha da chamada lista as propostas daquela chamada; a consulta de propostas ([PRD-06](./PRD-06-propostas.md)) é o caminho alternativo, para quando se busca a proposta e não a oportunidade.
- **Troca de chamada:** permitida somente enquanto a proposta não está submetida, e recalcula prazo, teto e documentos exigidos. Após a submissão o vínculo é imutável.
- **A chamada é a fonte das regras da proposta:** documentos obrigatórios, valor teto e prazo de submissão vêm da chamada vinculada, nunca de um cadastro global.
- **A proposta é a fonte dos indicadores da chamada:** a listagem e a ficha da chamada mostram a quantidade de propostas vinculadas por estado.
- **Usuário ativo:** a proposta exige um **usuário ativo** como coordenador para sair de rascunho e para ser submetida.

## Termos e decisões importantes

- **Chamada:** oportunidade externa (edital ou chamada pública). Situações: `aberta`, `encerrada`, `cancelada`.
- **Proposta:** entidade central do sistema, uma tentativa de captação em uma chamada. Estados: `rascunho`, `em preparação`, `submetida`, `arquivada`.
- **Arquivada:** estado único de encerramento da tentativa — toda proposta que não está em `rascunho`, `em preparação` ou `submetida` está `arquivada`. O arquivamento sempre registra um **motivo**: `prazo perdido` (a chamada encerrou sem submissão), `chamada cancelada` (a chamada vinculada foi cancelada) ou `desistência` (decisão da equipe, com texto obrigatório). Proposta arquivada é somente leitura e permanece consultável.
- **Resultado:** decisão da agência, aplicável somente a proposta `submetida`. Valores: `pendente`, `aprovado`, `reprovado`, `suplente`. É **informado manualmente** por um usuário do sistema, na ficha da proposta, e pode ser corrigido a qualquer momento — não existe integração com API ou serviço externo da agência que atualize o resultado automaticamente.
- **Número da proposta:** gerado **no momento da submissão**, no formato `AAAA/NNNN`, sequencial por ano e nunca reutilizado. O número marca o envio, não a aprovação: proposta submetida e depois reprovada mantém seu número. Proposta que nunca foi submetida não tem número.
- **Conteúdo congelado:** após a submissão, os dados da proposta (chamada, coordenador, tipo, título, resumo, duração, orçamento e documentos) tornam-se somente leitura. Continuam editáveis apenas observações internas, resultado, comprovante e o arquivamento por desistência — assim a alteração nunca muda a validade de uma submissão já feita.
- **Acompanhamento:** a tela inicial lista as chamadas com filtros por datas e por situação. Por padrão exibe apenas as chamadas `aberta`, ordenadas pela data de encerramento (vencimento) mais próxima — a rotina diária começa pelo prazo que vence primeiro.
- **Orçamento:** a chamada define um **valor teto**; a proposta informa um **valor total previsto**, validado contra esse teto. O valor previsto acima do teto é aviso na gravação e impedimento na submissão.
- **Prazo perdido:** decisão de lacuna — quando a data de encerramento da chamada passa e a proposta ainda não foi submetida, o sistema arquiva a proposta com motivo `prazo perdido` no primeiro acesso a ela (tela inicial, ficha da chamada, consulta ou ficha da proposta), registrando o evento no histórico. Não há processo agendado no MVP.
- **Cancelamento de chamada:** cancelar uma chamada arquiva, com motivo `chamada cancelada`, todas as propostas dela em `rascunho` ou `em preparação`. Propostas já submetidas não são afetadas. Reabrir a chamada (permitido enquanto a data de encerramento não passou) desarquiva as propostas arquivadas por esse motivo; propostas arquivadas por `prazo perdido` ou `desistência` não voltam.
- **Tipo da proposta:** lista fixa de três tipos (`projeto de pesquisa`, `apoio a bolsas`, `apoio a evento e infraestrutura`), cada um com campos extras apenas descritivos. O tipo não é um módulo separado: a troca é permitida somente antes da submissão e substitui o tipo anterior, descartando os campos extras do tipo abandonado.
- **Permissões:** não existem níveis de acesso. Qualquer usuário ativo e autenticado executa todas as operações do sistema.
- **Usuário inativo:** permanece no cadastro para preservar a autoria de ações antigas, não pode autenticar e não pode ser escolhido como coordenador de proposta não submetida.
- **Histórico:** registro simples e somente-leitura de eventos da proposta (criação, alterações de estado, submissão, resultado, arquivamento com motivo, desarquivamento), com autor e data/hora. Não é auditoria de campos.
- **Anexo de documento:** decisão de lacuna — upload de um arquivo por documento obrigatório da chamada, substituível enquanto a proposta não estiver submetida.
- **Fora de escopo do MVP:** relatórios, auditoria de campos, integração com agências (inclusive consulta automática de resultado), gestão financeira, recuperação de senha por e-mail e níveis de permissão.
