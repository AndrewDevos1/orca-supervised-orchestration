# Fluxo supervisionado

1. Confirmar autoridade do card, escopo, paths permitidos e WIP=1.
2. Verificar `orca status --json`, Runs, workers e worktrees.
3. Criar Run novo quando necessário.
4. Iniciar um worker com especificação autocontida no worktree autorizado.
5. Encerrar a vez do coordenador; não fazer polling.
6. No retorno legítimo, correlacionar Task/Dispatch, revisar worktree e executar validações exigidas.
7. Aceitar, rejeitar ou escalar pelo Gate de Integridade.

Worker falho, timeout ou trust bloqueado não autoriza retry automático. Preserve a primeira evidência e use a ajuda da CLI instalada para recuperação.