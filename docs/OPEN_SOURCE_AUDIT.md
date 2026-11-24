# Auditoria pré-exposição pública

Data: 2026-11-24. Escopo: todos os refs remotos e locais do repositório `AndrewDevos1/orca-supervised-orchestration`.

## Resultado

`OPEN_SOURCE_READY=BLOCKED` para tornar o repositório atual público.

## O que foi verificado

- Refs: `main` e `import/installed-skill-initial`.
- Histórico: três commits iniciais antes desta documentação.
- Integridade Git: `git fsck --no-reflogs --unreachable` sem objetos reportados.
- Varredura de padrões de segredo: sem chave privada, token GitHub, chave OpenAI, chave AWS ou segredo de formato conhecido.
- Licença declarada pela skill: MIT. Não foram identificados arquivos de terceiros no snapshot importado; o conteúdo é documentação e a skill atribuída ao proprietário.
- Comandos citados foram conferidos localmente: `orca status --json`, `orca repo list --json`, `orca skills get orchestration`, `orca orchestration run-create --help`, `worker-start --help`, `worker-list --help` e `worktree create --help`.

## Achados bloqueantes de privacidade

O histórico atual contém material inadequado para abrir diretamente:

1. e-mail pessoal do autor nos metadados Git dos commits;
2. paths pessoais WSL em `provenance/INSTALLED_SOURCE.md`;
3. path local em `provenance/SKILL.md.sha256`;
4. referência de path WSL na skill importada, necessária para explicar o boundary Windows/WSL, mas que deve ser generalizada para publicação pública.

Não foram detectadas credenciais, tokens, dados Saipos, dumps, logs de CRM ou conteúdo do 9Router. Isso não elimina a necessidade de revisão humana final.

## Estratégia segura de publicação pública

Não reescrever, apagar ou forçar o histórico privado atual. Preservá-lo como registro privado.

Criar um novo repositório público, ou uma nova origem pública, a partir de um único commit raiz limpo contendo apenas:

- `SKILL.md` revisado e generalizado, sem paths pessoais;
- README, LICENSE e documentação desta branch revisados;
- SHA-256 apresentado sem caminho local, se ainda for necessário;
- nenhum `.git` histórico, autor/e-mail pessoal, logs ou artefato de runtime.

Antes do push público: varrer novamente o diretório de exportação e inspecionar `git log`, `git show`, `git fsck` e todas as branches expostas. O proprietário decide o nome/origem pública e aprova o snapshot. A troca de visibilidade deste repositório privado não é recomendada enquanto esses achados existirem.

## Limitações

Esta auditoria não é uma garantia jurídica nem substitui análise de licenças de Hermes, Orca, Antigravity, modelos ou plataformas externas. Este projeto licencia somente sua própria documentação e skill conforme declaradas.
