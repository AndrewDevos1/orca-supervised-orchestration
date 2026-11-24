# Uso

## Preflight

Antes de criar Run ou worker, confirme que o card é autorizado, que não há executor mutável concorrente e que o worktree é permitido:

```sh
orca status --json
orca orchestration run-list --json
orca orchestration worker-list --json
orca worktree ps --json
```

O output pode conter histórico encerrado. Um worker ativo é o que exige WIP; `succeeded/failed` com `liveness=exited` e `terminalState=released` é histórico, não motivo para retry automático.

## Run e worker

Crie um Run novo para trabalho novo, não reutilize um Run de laboratório:

```sh
orca orchestration run-create --objective "CARD-123: objetivo autorizado" --json
```

Use os IDs retornados. Confirme a sintaxe efetiva:

```sh
orca orchestration worker-start --help
```

Exemplo de worker em worktree isolado:

```sh
orca orchestration worker-start \
  --spec "CARD-123: diagnóstico read-only. Veja USE-CASES.md para limites." \
  --worktree new-top-level --repo id:<repo-id> --name card-123-diagnosis \
  --agent antigravity --run <run-id> --json
```

O texto de `--spec` deve ser autocontido: objetivo, arquivos permitidos, caminhos protegidos, evidência esperada, proibições e se saída não-zero é falha ou evidência.

## Depois do retorno legítimo

Não faça polling. Quando Orca entregar `worker_done`, pergunta ou escalonamento, reconcilie o Dispatch:

```sh
orca orchestration worker-show --dispatch <dispatch-id> --json
```

Verifique worktree, `git status --short`, diffs rastreados, arquivos não rastreados e testes exigidos. Não aceite alteração em caminho protegido não autorizado.

## Dependências

Use dependências somente após confirmar que sua CLI as suporta:

```sh
orca orchestration worker-start --help
```

A CLI validada oferece `--task`, `--deps` e `--parent`; construa tarefas pequenas e valide a saída de N antes de iniciar N+1. Não assuma que uma receita de outra versão é compatível.

## Gate de Integridade

Rejeite, não promova, quando houver: mudança fora do allowlist, arquivo protegido alterado sem autorização, diff não explicado, teste ausente, segredo/dado real, ou evidência incompleta. Preserve o worktree e registre o motivo sanitizado.
