# Fluxo supervisionado

1. Confirmar autoridade do card, escopo, paths permitidos e WIP=1.
2. Verificar `orca status --json`, Runs, workers e worktrees.
3. Criar Run novo quando necessário.
4. Iniciar um worker com especificação autocontida no worktree autorizado.
5. Encerrar a vez do coordenador; não fazer polling.
6. No retorno legítimo, correlacionar Task/Dispatch, revisar worktree e executar validações exigidas.
7. Aceitar, rejeitar ou escalar pelo Gate de Integridade.

Worker falho, timeout ou trust bloqueado não autoriza retry automático. Preserve a primeira evidência e use a ajuda da CLI instalada para recuperação.

## Comunicação entre Hermes, Orca e worker

Uma solicitação não abre automaticamente um agente: o Hermes primeiro verifica se a tarefa está autorizada e se o ambiente está elegível. Quando a delegação é apropriada:

- **Run:** contexto da execução coordenada.
- **Task:** trabalho definido com escopo e critérios de aceite.
- **Dispatch:** tentativa de iniciar ou atribuir a Task a um worker concreto.
- **`worker_done`:** mensagem de resultado emitida pelo worker quando a execução chega ao fim; **não significa aceite do código**.

```mermaid
sequenceDiagram
    actor U as Desenvolvedor
    participant H as Hermes
    participant O as Orca CLI/runtime
    participant A as Agente executor
    U->>H: Solicita tarefa
    H->>H: Confere issue, escopo, autorização e WIP=1
    opt Gate humano obrigatório
        H-->>U: Explica a confirmação oficial necessária
        U-->>H: Informa que concluiu a ação
        H->>O: Verifica novamente o estado nativo
    end
    H->>O: Criar Run / Task / Dispatch autorizados
    O->>A: Inicia worker no worktree isolado
    Note over H,O: Hermes encerra o turno; sem polling contínuo
    opt Worker emite resultado
        A-->>O: worker_done com identificadores e outcome
    end
    alt Notificação efetivamente entregue
        O-->>H: Notificação / wake do coordenador
    else Sem notificação comprovada
        U->>H: Solicita reconciliação, se necessário
        H->>O: Consulta estado persistido do Dispatch
    end
    H->>O: Consulta tarefa, resultado e estado do worker
    H->>H: Revisa bytes, diffs, arquivos não rastreados e testes
    alt Critérios comprovados
        H-->>U: PASS com evidências
    else Defeito e correção autorizada
        H-->>U: REWORK com causa e escopo
    else Gate ou estado incerto
        H-->>U: BLOCKED; preserva evidências e worktree
    end
```

### Como o Hermes sabe que terminou?

O mecanismo desejado é **orientado a mensagens**: após o `worker-start`, o coordenador encerra seu turno. O Orca mantém o contexto e os resultados de Run/Task/Dispatch; quando um `worker_done` é emitido **e sua notificação chega**, o Hermes pode ser retomado para revisar. Não depende de ficar lendo continuamente o chat do agente.

A saída `--json` é uma representação retornada pela **CLI do Orca**, não um arquivo JSON que esta skill monitora ou modifica. Para reconciliação autorizada, use a ajuda da CLI instalada e os comandos `orca orchestration worker-show --dispatch <dispatch-id> --json` e `orca orchestration worker-list --json`. Leitura pontual da saída do worker, quando suportada, não é acompanhamento contínuo nem comprova resultado funcional.

A entrega automática de wake **não é garantida em todos os cenários**. Silêncio, timeout e falha de transporte não comprovam que o worker terminou. A recuperação segue [RECOVERY.md](RECOVERY.md); não duplicar Dispatches para contornar incerteza.

### Aceite não é conclusão do worker

O Hermes deve vincular resultado à Task e ao Dispatch corretos, inspecionar o worktree, verificar arquivos rastreados e não rastreados, confrontar testes e critérios da tarefa e então classificar `PASS`, `REWORK` ou `BLOCKED`. Um retorno `succeeded` do worker não substitui essa auditoria.
