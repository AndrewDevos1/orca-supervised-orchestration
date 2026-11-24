# Contribuindo

Obrigado por contribuir.

## Antes de abrir uma mudança

1. Abra uma issue descrevendo objetivo, limites e evidência esperada.
2. Mantenha uma mudança pequena e documentada.
3. Não inclua segredos, tokens, dados de produção, paths pessoais, logs brutos, credenciais, transcrições ou material de terceiros sem licença compatível.
4. Não altere o comportamento de segurança para contornar workspace trust, autenticação, permissões ou gates nativos.

## Desenvolvimento

Esta skill é Markdown. Valide links, exemplos de comandos e coerência com a CLI efetiva:

```sh
orca status --json
orca orchestration worker-start --help
orca orchestration worker-list --help
```

Comandos ilustrativos devem indicar pré-condições e não prometer execução sem autorização.

## Pull requests

Inclua:

- problema e escopo;
- arquivos alterados;
- comandos validados e versão/runtime usado;
- limitações não testadas;
- resultado de varredura de conteúdo sensível.

Um mantenedor revisa segurança, precisão e licença antes do merge. Não faça merge ou release por conta própria.
