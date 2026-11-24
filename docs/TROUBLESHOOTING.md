# Troubleshooting

## `orca status --json` não está pronto

Verifique se a CLI e o aplicativo Orca estão instalados e autenticados. Só prossiga quando o JSON indicar runtime alcançável e pronto. Não tente iniciar worker para diagnosticar um runtime indisponível.

## `agent-trust-workspace`

O gate ocorre antes da execução do worker. Registre o path Windows visível e solicite a confirmação interativa oficial para aquele path. Não use flags de bypass, não altere trust silenciosamente e não redirecione o trabalho para outro workspace.

## Worker falhou ou resposta foi perdida

Não repita `worker-start` inalterado. Consulte primeiro:

```sh
orca orchestration worker-list --json
orca orchestration worker-show --dispatch <dispatch-id> --json
```

Preserve `stage`, `lastError`, worktree e estado do terminal. Um worker com `liveness=exited` e `terminalState=released` é histórico encerrado. Com `unverifiable` ou `retained`, não assuma encerramento nem crie outro executor.

## Terminal ou sessão foram encerrados

Siga [RECOVERY](RECOVERY.md). Reconciliar é leitura; parar, liberar, resetar ou repetir são ações distintas e exigem decisão/autorização conforme o contexto.

## Sintaxe incompatível

A CLI evolui. Consulte a ajuda local antes de copiar exemplos:

```sh
orca orchestration worker-start --help
orca orchestration run-create --help
orca worktree create --help
```

Não invente flags. A ajuda do binário instalado é a fonte operacional.

## Diff inesperado ou arquivo protegido alterado

Não aceite o resultado. Preserve o worktree, registre `git status --short`, diff rastreado e arquivos não rastreados. Escalone para revisão humana.
