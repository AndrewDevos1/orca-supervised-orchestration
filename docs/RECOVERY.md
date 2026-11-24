# Recuperação sem duplicar trabalho

## Terminal ou sessão encerrados

Antes de retomar, executar somente leitura:

```sh
orca status --json
orca orchestration run-list --json
orca orchestration worker-list --json
orca worktree ps --json
```

Para cada Dispatch conhecido, usar `orca orchestration worker-show --dispatch <id> --json`.

## Decisão

- `state=succeeded` ou `failed`, `liveness=exited`, `terminalState=released`: histórico encerrado; não reiniciar automaticamente.
- `liveness=unverifiable` ou terminal `retained`: preservar; não assumir que está parado nem liberar/reutilizar sem decisão explícita.
- `agent-trust-workspace`: bloqueio anterior à execução. Registrar path Windows visível e solicitar confirmação interativa oficial; não alterar trust nem redirecionar o trabalho.
- Run histórico sem tarefas/dispatches: não é WIP ativo.

## Evidência mínima antes de novo dispatch

Registrar IDs de Run/Task/Dispatch, estado, `stage`, `lastError`, worktree, HEAD, `git status --porcelain` e alterações não rastreadas. Só então propor o menor escopo autorizado.

## Segurança

Nunca apagar worktree, resetar, matar terminal, repetir `worker-start` ou criar um segundo executor apenas por silêncio/timeout. Preservar a primeira falha e a evidência sanitizada.
