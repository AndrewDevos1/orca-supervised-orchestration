# Recuperação

Após perda de terminal/sessão, reconcilie antes de agir:

```sh
orca status --json
orca orchestration run-list --json
orca orchestration worker-list --json
orca worktree ps --json
orca orchestration worker-show --dispatch <dispatch-id> --json
```

- `exited` + `released`: histórico encerrado; não reinicie automaticamente.
- `unverifiable` ou `retained`: estado desconhecido; preserve, não duplique nem libere sem decisão.
- `agent-trust-workspace`: gate pré-execução; peça confirmação oficial para o path visível.

Registre IDs, `stage`, `lastError`, worktree, HEAD, status e arquivos não rastreados. Não resetar, remover worktree, matar terminal ou repetir worker por silêncio.