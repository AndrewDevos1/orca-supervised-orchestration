# Modelo operacional

Fluxo: proprietário → Hermes coordenador → Orca CLI → worker Antigravity → resultado persistido → Hermes valida artefatos e gates.

Invariantes:

- Orca nativo é a única camada de orquestração.
- WIP=1: um executor por card; nunca duplicar Dispatch aberto.
- O worker trabalha no próprio worktree.
- A autorização é específica; um worker não autoriza push, merge, deploy, cutover, banco ou credenciais.
- `worker_done` não é aceite: Hermes verifica diffs rastreados e não rastreados, testes e caminhos protegidos.
- Falhas preservam evidência. Não usar bypass de workspace trust, permissões ou segurança.
