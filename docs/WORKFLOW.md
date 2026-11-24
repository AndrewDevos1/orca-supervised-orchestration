# Fluxo supervisionado

## Pré-condições

1. Confirmar que o card autorizado é elegível.
2. Conferir `orca status`, Runs, workers e worktrees.
3. Confirmar WIP=1 e inexistência de Dispatch aberto para o card.
4. Criar ou usar somente o worktree isolado autorizado.
5. Escrever uma especificação autocontida: objetivo, arquivos permitidos, caminhos protegidos, evidências, proibições e limites.

## Execução

1. Criar um Run novo para trabalho de produto; nunca reutilizar Run de laboratório.
2. Iniciar um único worker Antigravity pelo Orca nativo.
3. Não iniciar polling. O retorno `worker_done`, pergunta ou escalonamento é o sinal para reconciliação.
4. Não usar bypass de workspace trust, permissões, autenticação ou segurança.

## Aceite

Após o sinal legítimo, correlacionar `task_id` e `dispatch_id`; verificar bytes reais no worktree, `git status --short`, diff rastreado e arquivos não rastreados. Testar o que a especificação exige. Um resultado de worker não substitui essa revisão.

## Proibições permanentes

Esta skill não autoriza push, merge, deploy, cutover, CI remoto, migration canônica, acesso a credenciais, sincronização produtiva ou operação no CRM. Cada ação requer autorização própria.
