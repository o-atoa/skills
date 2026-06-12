# Skill: Gerenciamento de Issues

## Objetivo
Orientar agentes de IA na criação, triagem, atualização e resolução de issues no GitHub de forma consistente e profissional.

## Quando usar
- Ao reportar um bug
- Ao solicitar uma funcionalidade
- Ao triar ou classificar issues existentes
- Ao resolver ou fechar uma issue

## Estrutura de uma Issue

### Bug Report
```markdown
### Descrição do Bug
[Descrição clara e concisa do bug]

### Passos para Reproduzir
1. Vá para '...'
2. Clique em '...'
3. Role até '...'
4. Veja o erro

### Comportamento Esperado
[O que deveria acontecer]

### Comportamento Atual
[O que realmente acontece]

### Evidências
[Logs, screenshots, vídeos]

### Ambiente
- SO: [ex: Ubuntu 22.04, Windows 11]
- Versão: [ex: v1.2.3]
- Browser: [ex: Chrome 120]

### Contexto Adicional
[Qualquer informação extra]
```

### Feature Request
```markdown
### Descrição da Funcionalidade
[O que você quer que seja implementado]

### Problema que Resolve
[Qual dor ou necessidade esta funcionalidade endereça]

### Comportamento Esperado
[Como a funcionalidade deve funcionar passo a passo]

### Alternativas Consideradas
[Outras abordagens que você pensou]

### Critérios de Aceitação
- [ ] Requisito 1
- [ ] Requisito 2
- [ ] Requisito 3
```

### Task / Chore
```markdown
### Descrição
[O que precisa ser feito]

### Motivação
[Por que isso é necessário]

### Checklist
- [ ] Passo 1
- [ ] Passo 2
- [ ] Passo 3

### Dependências
- #issue-número
```

## Boas Práticas

### Ao CRIAR uma issue
1. **Pesquise primeiro** — evite duplicatas. Busque issues existentes antes de abrir.
2. **Título claro e específico** — `feat: adicionar login com Google` e não `melhorias`
3. **Use labels** — `bug`, `enhancement`, `good first issue`, `help wanted`
4. **Atribua responsáveis** — sempre que possível
5. **Projeto e milestone** — mantenha o board atualizado

### Ao TRIAR issues
1. Verifique se a issue é válida e reproduzível
2. Adicione labels e prioridade (`priority: critical`, `priority: high`)
3. Atribua para a pessoa correta
4. Solicite informações faltantes com o template `incomplete`
5. Feche com `not planned` se não for aplicável

### Ao RESOLVER uma issue
1. **Link pelo commit** — use `Closes #123`, `Fixes #123`, `Resolves #123` na descrição do PR
2. **Referencie a issue** no corpo do PR
3. **Mantenha discussão na issue** — comentários técnicos profundos vão na issue, não no PR
4. **Atualize o status** ao mover para revisão

### Fluxo de Prioridades
```
P0 - Crítico: bloqueia produção → resolução imediata
P1 - Alto: funcionalidade quebrada → próxima sprint
P2 - Médio: melhoria desejada → backlog priorizado
P3 - Baixo: nice to have → backlog geral
```

## Labels Padrão

| Label | Cor | Descrição |
|-------|-----|-----------|
| `bug` | `#d73a4a` | Algo não está funcionando |
| `enhancement` | `#a2eeef` | Nova funcionalidade ou solicitação |
| `documentation` | `#0075ca` | Melhorias ou acréscimos na documentação |
| `good first issue` | `#7057ff` | Issue amigável para iniciantes |
| `help wanted` | `#008672` | Precisa de ajuda extra |
| `priority: critical` | `#b60205` | Deve ser resolvido imediatamente |
| `priority: high` | `#d93f0b` | Prioridade alta |
| `priority: medium` | `#fbca04` | Prioridade média |
| `priority: low` | `#0e8a16` | Prioridade baixa |
| `question` | `#d876e3` | Discussão ou dúvida |
| `wontfix` | `#ffffff` | Não será implementado |
| `duplicate` | `#cfd3d7` | Issue duplicada |
| `incomplete` | `#e4e669` | Faltam informações |

## Comandos Úteis (GitHub CLI)

```bash
# Criar issue
gh issue create --title "título" --body "descrição" --label "bug"

# Listar issues
gh issue list --label "bug" --assignee @me

# Ver detalhes
gh issue view 123

# Fechar issue
gh issue close 123 --comment "resolvido no PR #456"

# Reabrir
gh issue reopen 123

# Comentar
gh issue comment 123 --body "novas informações"
```
