# Casos de uso e prompts

Todos os exemplos exigem card autorizado e worktree permitido. Substitua placeholders; não copie para ambiente produtivo por inferência.

## Diagnóstico somente leitura

```text
Card: DOC-1. Diagnóstico read-only.
Objetivo: identificar arquivos que explicam o problema.
Permitido: ler README.md, executar git status --porcelain e git log -1.
Proibido: editar, testes, rede, banco, container, credenciais, push, merge, deploy.
Evidência: HEAD, status antes/depois, arquivos lidos e conclusão.
```

Aceite: status inalterado e evidência suficiente. Este resultado não autoriza correção.

## Mudança supervisionada

```text
Card: DOC-2. Corrigir um link quebrado em README.md.
Permitido: README.md somente.
Protegido: todos os demais caminhos.
Proibido: push, merge, deploy, comandos de banco/rede/credenciais.
Evidência: git diff -- README.md; comando de validação de links; git status --short.
```

O coordenador verifica o diff real antes de aceitar.

## Tarefas dependentes

Tarefa A cria uma evidência somente leitura. Tarefa B só pode iniciar depois de A validada pelo coordenador. Declare dependências usando exclusivamente a sintaxe que `orca orchestration worker-start --help` confirma no runtime alvo. Não use paralelismo para contornar WIP=1 em um mesmo card.

## Gate de Integridade

Antes do aceite, responder:

1. O Dispatch corresponde ao card e à Task esperados?
2. O worktree certo foi inspecionado?
3. Há mudança rastreada ou não rastreada fora do escopo?
4. Testes/evidências exigidos foram realmente executados?
5. Há segredos, dados reais ou paths sensíveis no resultado?

Qualquer resposta negativa é rejeição ou escalonamento, não retry automático.
