# Skill: Pull Requests

## Objetivo
Orientar agentes de IA na criação, revisão e merge de Pull Requests no GitHub de forma consistente, segura e profissional.

## Quando usar
- Ao criar um novo PR
- Ao revisar PRs de outros membros
- Ao fazer merge de PRs aprovados
- Ao lidar com conflitos ou CI falhando

## Estrutura de um Pull Request

### Template Padrão
```markdown
## Descrição

<!-- Explique o que este PR faz, por que é necessário e como foi implementado -->

Fecha ##<issue-número>

## Tipo de Mudança

- [ ] Bugfix
- [ ] Nova funcionalidade
- [ ] Mudança de quebra (breaking change)
- [ ] Refatoração
- [ ] Documentação
- [ ] Dependências

## Checklist

- [ ] Código segue o estilo do projeto
- [ ] Testes foram adicionados/atualizados
- [ ] Documentação foi atualizada
- [ ] TODOs foram resolvidos
- [ ] Não introduz novos warnings

## Evidências

<!-- Screenshots, logs, vídeos do antes/depois -->

## Contexto Adicional

<!-- Qualquer informação extra para o revisor -->
```

## Boas Práticas

### Ao CRIAR um PR

1. **PRs pequenos e focados** — máximo de 200-400 linhas. PRs gigantes são difíceis de revisar.
2. **Um PR resolve uma coisa** — um PR = uma feature ou um bug. Evite PRs com múltiplos objetivos.
3. **Título descritivo** — `feat(auth): implementa login com Google OAuth` em vez de `update auth`
4. **Link a issue** — use `Closes #123`, `Fixes #456` no corpo da descrição
5. **Descrição rica em contexto** — explique o *porquê* da mudança, não apenas o *que* mudou
6. **Mantenha o diff limpo** — rebase contra a branch alvo, evite commits de merge
7. **Commits organizados** — commits atômicos com mensagens claras seguindo conventional commits

### Convenção de Commits

```
feat(escopo): descrição no imperativo
fix(escopo): descrição no imperativo
refactor(escopo): descrição
docs(escopo): descrição
test(escopo): descrição
chore(escopo): descrição
```

### Ao REVISAR um PR

1. **Seja construtivo** — critique o código, não a pessoa
2. **Explique o porquê** — sugira *por que* algo deveria mudar
3. **Use `suggestion`** no GitHub para oferecer código pronto
4. **Diferencie blockers de nits**:
   - **Blocker**: bug, segurança, quebra de contrato — precisa ser resolvido
   - **Nit**: estilo, preferência pessoal — pode ser ignorado
5. **Aprove ou solicite mudanças** claramente — use o GitHub Review

### Exemplo de Review Construtivo

```markdown
Boa implementação! Alguns pontos:

**Blocker**: A senha está sendo logada em caso de erro (linha 42). 
Vazamento de informação sensível. Sugiro remover ou mascarar.

**Nit**: O nome da função `process_data` é genérico. Que tal 
`validate_user_input` para ser mais descritivo?
```

### Ao FAZER MERGE

1. **Squash and Merge** para PRs com múltiplos commits pequenos (padrão recomendado)
2. **Rebase and Merge** para PRs com commits atômicos bem organizados
3. **Merge Commit** para PRs onde cada commit é significativo e independente
4. **Nunca se auto-merge** sem aprovação de pelo menos 1 revisor
5. **Delete a branch** após o merge

## Fluxo CI/CD

```yaml
# Exemplo de esteira ideal
pr:
  - lint:     # verifica estilo (eslint, prettier, ruff)
  - typecheck # verifica tipos (TypeScript, mypy)
  - test:     # roda testes unitários e de integração
  - build:    # verifica se compila
  - coverage  # verifica cobertura mínima
```

### O que fazer quando o CI falha

1. **Leia o log** — o erro geralmente está nas últimas linhas
2. **Identifique** se é erro de código ou infraestrutura (flaky test)
3. **Corrija** e faça push de um novo commit
4. **Se for flaky test**, tente rodar novamente (gh run rerun)
5. **Documente** flaky tests como issue

## Resolução de Conflitos

```bash
# Atualizar branch com alvo
git checkout minha-branch
git fetch origin
git rebase origin/main

# Resolver conflitos manualmente, depois:
git add .
git rebase --continue

# Ou usar merge (prefira rebase)
git merge origin/main

# Forçar push após rebase (cuidado!)
git push --force-with-lease
```

## Comandos Úteis (GitHub CLI)

```bash
# Criar PR
gh pr create --title "feat: descrição" --body "body.md" --assignee @me

# Listar PRs
gh pr list --review-requested @me

# Ver detalhes
gh pr view 123

# Checkout de PR localmente
gh pr checkout 123

# Revisar (approve)
gh pr review 123 --approve --body "LGTM!"

# Solicitar mudanças
gh pr review 123 --request-changes --body "precisa ajustar X"

# Fazer merge
gh pr merge 123 --squash --delete-branch

# Ver status CI
gh pr checks 123
```
