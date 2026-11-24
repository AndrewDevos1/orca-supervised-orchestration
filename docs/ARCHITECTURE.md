# Arquitetura

```text
Proprietário → Hermes (coordenador) → Orca CLI → Run/Task/Dispatch → worker → resultado
                                      ↓                              ↓
                                  worktree isolado              Gate de Integridade
```

Hermes decide e revisa; Orca fornece ciclo de vida nativo; o worker executa somente o escopo autorizado. O card/issue existente permanece fonte de verdade.

A skill não substitui autorização humana, revisão de código, testes, controles de acesso ou políticas dos produtos envolvidos.

## Fronteiras

- Cada worker usa worktree próprio quando há mudança.
- Um card não recebe executor concorrente.
- Credenciais, banco, rede, deploy, CI, push e merge são proibidos salvo autorização específica.
- Paths entre WSL e Windows são fronteira de trust, não atalho operacional.
