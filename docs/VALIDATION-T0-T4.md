# Validação T0–T4

## Evidência disponível

A distribuição foi preparada com Orca CLI `1.4.248`. Foram consultados com sucesso:

- `orca status --json`;
- `orca repo list --json`;
- `orca skills get orchestration`;
- ajuda de `run-create`, `worker-start`, `worker-list` e `worktree create`.

Histórico laboratorial anterior relatou tarefas T0–T4, incluindo detecção posterior de alteração protegida. Esse material não foi copiado para evitar paths, logs e detalhes privados.

## Limites

T0–T4 não provam bloqueio de toda escrita em tempo real, execução remota, wake automático, comportamento de todos os agentes, nem aceitação de produto. `worker_done` sempre requer Gate de Integridade.

## Próxima validação reproduzível

Somente com autorização: criar um repositório descartável, executar um diagnóstico read-only, registrar Run/Task/Dispatch, verificar worktree antes/depois e testar um diff deliberado em arquivo protegido sem promovê-lo. Não use repositório produtivo.