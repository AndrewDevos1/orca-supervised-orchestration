# Contribuindo

Abra uma issue antes de mudança maior. Pull requests devem ser pequenos, explicitar objetivo, arquivos, comandos validados, limitações e resultado de varredura de conteúdo sensível.

Não envie credenciais, tokens, dados reais, paths pessoais, logs/transcrições brutos ou material de terceiros sem licença compatível.

Não reduza guardas de workspace trust, autenticação, permissões, isolamento ou revisão de integridade.

Valide a sintaxe contra a CLI efetiva:

```sh
orca status --json
orca orchestration worker-start --help
orca orchestration worker-list --help
```

Mantenedores revisam precisão, segurança e licença. Contribuidores não devem fazer merge ou release por conta própria.