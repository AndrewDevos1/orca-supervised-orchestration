# Instalação

## Requisitos obrigatórios

- Hermes Agent com suporte a skills;
- Orca CLI instalado, autenticado e acessível no ambiente em que Hermes executa comandos;
- Git;
- acesso ao repositório/worktree que será supervisionado.

A skill não instala Hermes, Orca nem Antigravity. Confirme a instalação real antes de continuar:

```sh
orca status --json
orca repo list --json
orca skills get orchestration
```

A saída de `orca status --json` deve indicar runtime alcançável e pronto. Se não indicar, não inicie worker para diagnosticar instalação.

## Instalar a skill

Clone ou baixe este repositório e copie somente `SKILL.md` para o diretório de skills do perfil Hermes. Exemplo comum em Linux/WSL:

```sh
mkdir -p ~/.hermes/skills/autonomous-ai-agents/orca-supervised-orchestration
cp SKILL.md ~/.hermes/skills/autonomous-ai-agents/orca-supervised-orchestration/SKILL.md
```

Abra uma nova sessão Hermes para carregar a skill. Compare antes de sobrescrever uma instalação existente:

```sh
sha256sum SKILL.md ~/.hermes/skills/autonomous-ai-agents/orca-supervised-orchestration/SKILL.md
```

## Linux/WSL + Orca no Windows + Antigravity

Este cenário foi usado para validar a documentação de comandos, não como homologação total:

- Hermes e a skill: Linux/WSL;
- Orca app/runtime: Windows host, acessível pelo CLI no ambiente Hermes;
- agente: `antigravity`, disponibilizado pelo Orca;
- repositório: deve ser registrado no Orca e ter um path Windows visível quando o worker será iniciado no host Windows.

Antes de um worker, valide o mesmo runtime pelo caminho efetivo e confirme o path que o Orca mostra. Um workspace atravessando Windows/WSL pode exigir confirmação interativa de trust. Isso é obrigatório quando solicitado; não existe bypass suportado por esta skill.

## Opcionais

- Um card/issue nativo para servir de fonte de verdade;
- worktree Orca separado para trabalho mutável;
- GitHub CLI para publicar revisões humanas. Não é necessário para usar a skill.

## Não testado / sem promessa

- instalação automática em um comando;
- macOS, Linux sem WSL, ou servidores Orca remotos;
- todos os modelos/agentes e opções de wake;
- configuração de trust por script;
- compatibilidade com versões futuras da CLI.

Consulte `orca ... --help` no ambiente alvo. A ajuda local é a fonte de sintaxe.