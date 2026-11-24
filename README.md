# Orca Supervised Orchestration

Skill experimental para Hermes coordenar workers, incluindo Antigravity, pelo Orca CLI nativo. O foco é supervisão: autorização explícita, worktrees isolados, WIP=1, evidência verificável e revisão humana após cada worker.

## Estado

Experimental. A documentação foi validada contra Orca CLI `1.4.248` em ambiente Linux/WSL com Orca alcançável. Isso não prova compatibilidade universal, wake automático, execução remota, nem homologação em todos os sistemas.

## O que este projeto é — e não é

Esta distribuição fornece `SKILL.md` e documentação sob [MIT](LICENSE). Não torna Hermes, Orca, Antigravity, modelos, serviços ou dependências de terceiros open source. Use cada produto sob seus próprios termos.

Não cria daemon, cron, servidor, dashboard, Kanban paralelo ou automação autônoma. Não autoriza push, merge, deploy, cutover, banco, credenciais, sincronização produtiva ou bypass de trust/permissões.

## Começar

1. Leia [INSTALL.md](INSTALL.md).
2. Verifique seu runtime com os comandos de [USAGE.md](USAGE.md).
3. Use os exemplos de [USE-CASES.md](USE-CASES.md) somente em worktree e card autorizados.
4. Antes de recuperar sessão/terminal, leia [docs/RECOVERY.md](docs/RECOVERY.md).

## Conteúdo

- [Arquitetura](docs/ARCHITECTURE.md)
- [Fluxo supervisionado](docs/WORKFLOW.md)
- [Troubleshooting](docs/TROUBLESHOOTING.md)
- [Validação T0–T4](docs/VALIDATION-T0-T4.md)
- [Manutenção](docs/MAINTENANCE.md) e [versionamento](docs/VERSIONING.md)
- [Contribuição](CONTRIBUTING.md), [segurança](SECURITY.md) e [código de conduta](CODE_OF_CONDUCT.md)

## Limitações importantes

- `worker_done` não equivale a aceite; valide worktree, diffs, arquivos não rastreados e testes.
- Timeout, silêncio e `liveness=unverifiable` não provam que um worker parou. Não duplique executores.
- `agent-trust-workspace` é gate humano para um path. Não contorne nem altere trust silenciosamente.
- A ajuda da CLI instalada prevalece sobre exemplos estáticos desta distribuição.

## Licença

[MIT](LICENSE), Copyright (c) 2026 AndrewDevos1.
