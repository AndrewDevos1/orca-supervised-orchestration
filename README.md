# Orca Supervised Orchestration

Uma skill para Hermes coordenar workers Antigravity pelo Orca CLI nativo, com autorização explícita, worktrees isolados, WIP=1 e verificação posterior de evidências.

Status: experimental. A skill documenta um fluxo supervisionado; não declara suporte estável nem garante compatibilidade com versões futuras de Hermes, Orca ou Antigravity.

## Escopo

Este projeto disponibiliza a skill e sua documentação sob [MIT](LICENSE). Ele não torna Hermes, Orca, Antigravity, modelos, serviços, aplicativos de terceiros ou suas dependências open source. Use-os sob seus próprios termos e políticas.

A skill não cria daemon, cron, servidor, dashboard ou Kanban próprio. O card existente continua como fonte de verdade.

## Instalação

Pré-requisitos:

- Hermes Agent em Linux/WSL;
- Orca CLI instalado e autenticado;
- um agente Orca disponível, por exemplo `antigravity`;
- Git.

Copie `SKILL.md` para o diretório de skills do seu perfil Hermes, por exemplo:

```sh
mkdir -p ~/.hermes/skills/autonomous-ai-agents/orca-supervised-orchestration
cp SKILL.md ~/.hermes/skills/autonomous-ai-agents/orca-supervised-orchestration/SKILL.md
```

Reinicie ou abra uma nova sessão Hermes conforme sua instalação. Não sobrescreva uma skill já instalada sem comparar hash e conteúdo.

## Configuração e verificação

A skill não requer variáveis de ambiente próprias. Confirme o runtime e os repositórios Orca:

```sh
orca status --json
orca repo list --json
orca skills get orchestration
```

Espere `runtime.state=ready` e `runtime.reachable=true`. A descoberta de repositórios não autoriza workers nem escrita.

## Uso supervisionado

Antes de iniciar um worker: confirme card/objetivo autorizado, worktree permitido, WIP=1, ausência de Dispatch aberto e caminhos protegidos.

```sh
orca orchestration run-create --objective "<card-id>: <objetivo>" --json
orca orchestration worker-start \
  --spec "<escopo autocontido; arquivos permitidos; proibições; evidências>" \
  --worktree new-top-level --repo id:<repo-id> --name <nome-curto> \
  --agent antigravity --run <run-id> --json
```

Use IDs retornados pelo Orca. A sintaxe pode variar entre versões; confirme com:

```sh
orca orchestration worker-start --help
orca orchestration worker-list --json
orca orchestration worker-show --dispatch <dispatch-id> --json
```

Não faça polling. Orca deve entregar `worker_done`, pergunta ou escalonamento. Depois, valide bytes no worktree, `git status --short`, diff rastreado, arquivos não rastreados e testes exigidos.

## Exemplo: diagnóstico sem escrita

Especificação mínima:

```text
Card: EXEMPLO-1. Diagnóstico somente leitura.
Worktree: isolado e autorizado.
Permitido: ler README.md e executar git status --porcelain.
Proibido: editar, teste, rede, banco, container, credenciais, push, merge, deploy.
Evidência: registrar HEAD, git status antes/depois e arquivos lidos.
```

A aprovação de um diagnóstico não autoriza uma mudança posterior.

## Limitações conhecidas

- `worker_done` não prova qualidade nem aceite; requer revisão independente.
- Silêncio, timeout ou `liveness=unverifiable` não provam encerramento; não crie executor duplicado.
- `agent-trust-workspace` é gate humano para o path Windows visível. Não contorne trust, permissões ou segurança.
- O fluxo foi validado contra a CLI Orca presente na publicação; flags e comportamento podem mudar.
- Recursos de execução remota, wake automático e release de terminal dependem do runtime Orca e não são garantidos por esta skill.

## Manutenção

- Atualize exemplos após validar `orca ... --help` na versão efetiva.
- Registre versão, IDs de Run/Task/Dispatch, SHA do worktree e evidências sanitizadas.
- Antes de publicar mudanças, faça varredura de segredos, caminhos pessoais, logs, dados de cliente e referências privadas.
- Consulte [WORKFLOW](docs/WORKFLOW.md) e [RECOVERY](docs/RECOVERY.md).

## Contribuir e segurança

Leia [CONTRIBUTING.md](CONTRIBUTING.md), [SECURITY.md](SECURITY.md) e [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md). Não envie tokens, dados reais, paths pessoais, transcrições de runtime ou logs não sanitizados.

## Licença

[MIT](LICENSE), Copyright (c) 2026 AndrewDevos1.
