## Context

Ver `proposal.md` - Why. O projeto ainda não tinha stack registrada; a decisão foi tomada em conversa com o responsável pelo produto, avaliando opções de front-end, back-end e banco relacional para um sistema pequeno, priorizando velocidade de desenvolvimento e infraestrutura mínima.

## Goals / Non-Goals

**Goals:**
- Fixar Next.js full-stack (front + back no mesmo projeto) e PostgreSQL como stack aprovada, com racional documentado para orientar decisões futuras.
- Deixar explícitas as alternativas descartadas e por quê, evitando que voltem a ser propostas sem justificativa nova.

**Non-Goals:**
- Não define ORM, biblioteca de UI, estratégia de autenticação, hospedagem/deploy ou CI/CD — ficam para changes de implementação subsequentes.
- Não faz o scaffolding do projeto nem configura o banco — apenas registra a decisão na Constituição.

## Decisions

**Next.js full-stack (front + back no mesmo projeto) em vez de back-end separado (NestJS/FastAPI).**
Racional: para o porte do sistema, um único projeto reduz superfície de deploy, configuração e manutenção (um só processo, um só pipeline). API Routes/Route Handlers e Server Actions cobrem as necessidades de back-end sem precisar de um serviço adicional.
Alternativa rejeitada: back-end dedicado em Node (NestJS) ou Python (FastAPI) — traria melhor separação de responsabilidades e escalaria melhor para um time grande ou domínio complexo, mas para este sistema pequeno o custo de operar dois serviços não se paga.

**Java descartado.**
Racional: overhead de setup, build e verbosidade incompatível com a meta de desenvolvimento rápido para um sistema pequeno.
Alternativa rejeitada: nenhuma variante de Java (Spring Boot etc.) foi considerada viável frente ao critério de velocidade de entrega.

**PostgreSQL em vez de SQLite/libSQL como banco principal.**
Racional: Postgres é rápido o suficiente para a carga esperada, suporta múltiplas conexões concorrentes sem a limitação de escrita única do SQLite, e não impõe restrição de topologia de deploy (funciona igual em serverless, container ou VPS).
Alternativa rejeitada: SQLite/libSQL (Turso) — reduziria a infraestrutura a zero (arquivo local) e teria a menor latência possível para carga baixa, mas exige ou um serviço de réplica remota (libSQL/Turso) para funcionar em deploy serverless/edge, ou renunciar a esse tipo de deploy; e trava escritas concorrentes, o que é uma restrição a menos com Postgres.

## Riscos / Trade-offs

- **Risco:** um único projeto Next.js mistura responsabilidades de front e back, podendo dificultar a evolução se o sistema crescer além do previsto. → **Mitigação:** manter a lógica de acesso a dados e regras de negócio isolada em módulos próprios (ex.: camada de `services`/`repositories`), para que uma eventual extração para um back-end separado não exija reescrever a lógica, só o transporte.
- **Risco:** operar Postgres exige um serviço de banco gerenciado ou container, ao contrário de SQLite (que não exige infraestrutura). → **Mitigação:** usar um provedor gerenciado (ex.: instância Postgres em núvem com camada gratuita/baixo custo) ou um único container Docker ao lado da aplicação, mantendo a operação simples mesmo para um sistema pequeno.

## Dúvidas em aberto

- Qual será o provedor/hospedagem do Postgres (gerenciado vs. container próprio) — decisão de infraestrutura a resolver na change de scaffolding/deploy, não altera a escolha de stack registrada aqui.
