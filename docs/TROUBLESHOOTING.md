# Troubleshooting

## Runtime indisponível

Rode `orca status --json`. Se o runtime não estiver pronto/alcançável, corrija a instalação/autenticação do Orca; não inicie worker como sonda.

## Workspace trust

`agent-trust-workspace` ocorre antes do worker. Confirme interativamente o path correto. Não use bypass ou alteração silenciosa de trust.

## Erro de worker

Use `worker-list` e `worker-show`. Preserve `stage` e `lastError`. Não repita o comando idêntico antes de entender o erro.

## Receita incompatível

Rode `orca orchestration worker-start --help`. A ajuda do binário local prevalece; versões evoluem.

## Diff inesperado

Preserve o worktree, registre `git status --short`, diff rastreado e arquivos não rastreados. Rejeite mudanças fora do escopo.