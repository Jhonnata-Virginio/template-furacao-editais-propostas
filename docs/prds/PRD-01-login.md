# PRD — Acesso ao sistema

## Objetivo

Permitir que um membro da equipe se autentique com e-mail e senha para usar o sistema, garantindo que apenas usuários ativos entrem e que toda ação registrada tenha autoria conhecida.

## Usuário e contexto

Membros da equipe técnica e administrativa do Laboratório IDE.IA. Todos têm a mesma rotina e o mesmo acesso: não há níveis de permissão. É a primeira tela do sistema e a única acessível sem sessão ativa.

## Comportamento esperado

- Formulário com **e-mail** e **senha**, ambos obrigatórios, e ação **Entrar**.
- O e-mail identifica o acesso e é único no cadastro; a comparação ignora maiúsculas/minúsculas e espaços nas extremidades.
- A senha é armazenada protegida (hash) e nunca exibida ou retornada pelo sistema.
- Credenciais inválidas exibem mensagem genérica ("E-mail ou senha inválidos"), sem revelar se o e-mail existe.
- Usuário com status inativo não autentica, mesmo com senha correta, e recebe a mesma mensagem genérica.
- Autenticação bem-sucedida cria a sessão e redireciona para a tela inicial de chamadas (`/`).
- Enquanto a requisição está em andamento, a ação **Entrar** fica desabilitada com indicação de carregamento, evitando envio duplicado.
- Acesso a qualquer rota interna sem sessão ativa redireciona para `/login`.
- Encerrar sessão (ação disponível no cabeçalho das telas internas) invalida a sessão e retorna a esta tela.

## Navegação e integrações

- **De onde vem:** entrada direta na aplicação ou redirecionamento de rota protegida sem sessão.
- **Para onde vai:** tela inicial de chamadas (`/`) após autenticação.
- **Depende de:** cadastro de usuários ([PRD-03](./PRD-03-usuarios.md)), que define e-mail, senha e status ativo.

## Critérios de aceite

- **Dado que** informei e-mail e senha de um usuário ativo, **quando** confirmo **Entrar**, **então** a sessão é criada e vejo a listagem de chamadas abertas.
- **Dado que** informei a senha errada, **quando** confirmo **Entrar**, **então** permaneço na tela com a mensagem "E-mail ou senha inválidos" e o campo de senha limpo.
- **Dado que** meu usuário está inativo, **quando** informo credenciais corretas e confirmo **Entrar**, **então** o acesso é negado com a mesma mensagem genérica.
- **Dado que** deixei e-mail ou senha em branco, **quando** confirmo **Entrar**, **então** o campo vazio é destacado e nenhuma requisição é enviada.
- **Dado que** não tenho sessão ativa, **quando** acesso `/propostas`, **então** sou redirecionado para `/login`.

## Observações e decisões

- Recuperação de senha por e-mail está fora do escopo do MVP: a senha é definida ou redefinida por qualquer usuário ativo na tela de Usuários.
- Não há cadastro público nem autoatendimento: o primeiro usuário é criado na carga inicial do sistema.
- Não há bloqueio por tentativas nem autenticação em dois fatores no MVP, dado o número pequeno de usuários.
