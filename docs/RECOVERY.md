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

## Fluxograma de recuperação segura

O diagrama representa **decisões de supervisão**, não comandos de bypass ou um mecanismo de retomada automática garantido.

```mermaid
flowchart TD
    I["Falha de trust, ausência de worker_done ou sessão interrompida"] --> S["Reconciliar Run, Task, Dispatch e worktree em modo leitura"]
    S --> T{"Erro nativo agent-trust-workspace?"}
    T -->|Sim| H["Mostrar caminho exato e ação no aplicativo oficial"]
    H --> U{"Usuário informou que confirmou?"}
    U -->|Não| P["WAITING_HUMAN: preservar estado"]
    U -->|Sim| V["Verificar trust real, Dispatch anterior e WIP=1"]
    V --> R{"Estado confirmado e retry nativo permitido?"}
    R -->|Sim| X["Retomar com único despacho autorizado"]
    R -->|Não| P
    T -->|Não| W{"Worker ainda ativo?"}
    W -->|Sim| A["Preservar WIP=1 e aguardar notificação; sem polling"]
    W -->|Não| E{"Encerrado e terminal released?"}
    E -->|Sim| D["Verificar resultado, arquivos, diffs e testes"]
    E -->|Não ou incerto| K["Preservar sem reiniciar; informar bloqueio"]
    D --> F{"Entrega comprovada?"}
    F -->|Sim| OK["Registrar PASS com evidências"]
    F -->|Não| NOK["REWORK ou BLOCKED após análise do escopo"]
```

### Intervenção humana assistida

Quando o gate nativo for `agent-trust-workspace`, o Hermes deve apresentar o **caminho exato visível ao aplicativo**, o motivo, a ação oficial necessária e, quando disponível, um link/comando de abertura comprovado. O usuário confere a pasta e só então escolhe a opção de confiança no aplicativo. Responder `pronto`, `feito` ou `confiado` **apenas solicita nova verificação do estado**, não concede trust automaticamente.

Após a confirmação, consultar o estado real e os identificadores do Dispatch anterior. Só fazer um novo despacho ou um retry nativo se a situação estiver reconciliada, o comando for permitido pela CLI efetivamente instalada e não existir executor concorrente. Nunca alterar configurações de confiança, mascarar erro ou migrar de workspace para evitar o gate.

### Sem resposta ou sem notificação

Se o `worker_done` não chegar, não assumir que o worker parou. Diferencie processo ativo, `exited/released` e `unverifiable/retained`; preserve worktree, IDs, status e evidências. Uma consulta manual ao estado **não comprova** que a notificação automática funcionou.
