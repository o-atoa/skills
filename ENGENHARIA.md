# ENGENHARIA — Workflow de Engenharia de Software

> Guia completo do ciclo de vida de desenvolvimento de software (SDLC): planejamento, implementação, testes, deploy e manutenção. Baseado em práticas oficiais de Google, Microsoft, GitHub e ThoughtWorks.

---

## Ciclo de Desenvolvimento

### Fases
1. **Análise** → entender requisitos, explorar código, identificar impacto
2. **Planejamento** → arquitetar solução, dividir em tarefas, estimar
3. **Implementação** → codificar seguindo convenções, uma mudança por vez
4. **Verificação** → testar, lintar, typecheck, revisar
5. **Entrega** → commit, PR, deploy, documentar
6. **Iteração** → feedback, ajustes, melhoria contínua

### Portão de Qualidade
Antes de cada entrega, passe por este portão:

```
Análise → Planejamento → Implementação → [Lint + Typecheck + Testes] → Commit → PR → CI → Deploy
                                      ↑
                         Falhou? Corrija e volte
```

---

## Análise e Exploração

### Entendendo o Contexto
Antes de escrever qualquer código:

1. Leia o `README.md` — propósito, setup, scripts
2. Verifique `AGENTS.md` ou `CONTRIBUTING.md` — convenções do projeto
3. Identifique o gerenciador de pacotes (package.json, Cargo.toml, pyproject.toml)
4. Entenda a estrutura de diretórios
5. Leia arquivos similares ao que vai modificar
6. Identifique dependências e imports existentes
7. Verifique testes existentes para o módulo
8. Busque por issues/PRs relacionados

### Mapeamento de Impacto
Para cada mudança, identifique:
- **Arquivos a modificar**: quais exatamente
- **Arquivos a criar**: se necessário
- **Arquivos a deletar**: se houver
- **Dependências**: adicionar ou remover
- **Testes**: quais criar ou atualizar
- **Documentação**: o que precisa ser atualizado

---

## Planejamento

### Divisão de Tarefas
- Quebre o trabalho em unidades atômicas (máx 200 linhas por commit)
- Priorize por dependência (o que vem primeiro)
- Identifique riscos (partes desconhecidas, complexas ou sensíveis)
- Defina critérios de aceitação claros

### Estratégia de Implementação
| Complexidade | Abordagem |
|-------------|-----------|
| **Simples** (1-2 arquivos) | Implementação direta |
| **Média** (3-5 arquivos) | Plano rápido + implementação |
| **Complexa** (5+ arquivos) | Plano detalhado + aprovação |
| **Incerta** | Protótipo/POC primeiro |

---

## Implementação

### Fluxo de Trabalho

1. **Sincronize** o repositório (`git pull --ff-only`)
2. **Crie branch** de funcionalidade
3. **Implemente** em commits atômicos
4. **Verifique** após cada commit (lint + typecheck)
5. **Teste** localmente antes de push
6. **Push** e abra PR

### Regras de Ouro
- **Nunca** modifique lockfiles manualmente
- **Nunca** use `git add .` — adicione arquivos intencionalmente
- **Nunca** force push
- **Nunca** edite testes para fazê-los passar — corrija o código
- **Nunca** hardcode secrets ou credenciais
- **Sempre** verifique `git diff --cached` antes de commitar
- **Sempre** leia o código existente antes de editar
- **Sempre** verifique dependências antes de usar uma lib
- **Máximo 3 tentativas** no mesmo erro — depois peça ajuda

### Depuração Eficiente

1. **Reproduza** o problema (passo a passo)
2. **Isole** a causa (minimize o cenário)
3. **Formule** hipóteses (o que pode estar errado?)
4. **Teste** cada hipótese (uma de cada vez)
5. **Adicione logs** descritivos para rastrear estado
6. **Consulte** documentação, issues, Stack Overflow
7. **Corrija** a causa raiz, não o sintoma
8. **Verifique** se a correção funciona e não quebra outros cenários

---

## Verificação

### Testes
| Camada | O que testar | Ferramentas |
|--------|-------------|-------------|
| **Unitário** | Lógica pura, utils, hooks, funções | Vitest, Jest, Pytest |
| **Integração** | APIs, banco, serviços, fluxos | Supertest, Testcontainers |
| **E2E** | Fluxos críticos do usuário | Playwright, Cypress |
| **Snapshot** | Componentes estáveis (use com moderação) | Vitest/Jest snapshots |

### Cobertura Mínima
- Linhas: 85% (aceitável 75%)
- Branches: 80% (aceitável 70%)
- Funções: 90% (aceitável 80%)

### Verificações Obrigatórias
1. Lint (eslint, ruff, clippy, flake8)
2. Typecheck (tsc, mypy, go vet, cargo check)
3. Testes unitários
4. Testes de integração
5. Build (`npm run build`, `cargo build`, etc.)
6. Segurança (secrets scanning se disponível)

---

## Deploy

### Pipeline CI/CD Ideal
```yaml
stages:
  - lint
  - typecheck
  - test
  - build
  - security_scan
  - deploy_staging
  - integration_tests
  - deploy_production
```

### Pré-Deploy
- [ ] Todos os testes passam
- [ ] Lint e typecheck verdes
- [ ] Build bem-sucedido
- [ ] Dependências atualizadas
- [ ] Migrações de banco preparadas
- [ ] Variáveis de ambiente configuradas
- [ ] Checklist de segurança revisado

### Estratégias de Deploy
| Estratégia | Risco | Complexidade | Uso |
|-----------|-------|-------------|-----|
| **Rolling update** | Baixo | Média | Aplicações stateless |
| **Blue-green** | Muito baixo | Alta | Sistemas críticos |
| **Canary** | Baixo | Alta | Teste em produção gradual |
| **Feature flag** | Mínimo | Média | Liberação controlada |
| **Recreate** | Alto | Baixa | Ambientes pequenos |

---

## Monitoramento e Operação

### Métricas Essenciais
- **Latência** (p50, p95, p99)
- **Taxa de erro** (HTTP 5xx, exceções)
- **Throughput** (requests/s, transações/s)
- **Disponibilidade** (uptime, SLO/SLI)
- **Recursos** (CPU, memória, disco, rede)

### Logs
- Estruturados (JSON) e pesquisáveis
- Níveis: DEBUG < INFO < WARN < ERROR < FATAL
- Contexto: request ID, correlação, timestamp
- Nunca logue dados sensíveis (senhas, tokens, PII)

### Alertas
| Severidade | Resposta | Exemplo |
|-----------|----------|---------|
| **P0/Critical** | Imediata (24/7) | Site fora do ar |
| **P1/High** | Próximas 4h | Feature quebrada |
| **P2/Medium** | Próximo dia útil | Performance degradada |
| **P3/Low** | Backlog | Melhoria desejada |

---

## Gestão de Configuração

### Variáveis de Ambiente
- Nunca hardcode no código fonte
- Use `.env` para desenvolvimento local
- Documente todas as variáveis em `.env.example`
- Secretos: use vault, secrets manager, ou CI secrets

### Versionamento de Configuração
- Arquivos de configuração versionados: docker-compose, CI configs
- Secrets NUNCA versionados
- Feature flags versionadas e rastreáveis

---

## Segurança no Ciclo de Vida

### Análise Estática (SAST)
- **ESLint** com plugins de segurança
- **Semgrep** ou **CodeQL** para padrões de vulnerabilidade
- **Trivy** para scans de container/imagem
- **Dependabot** ou **Renovate** para dependências

### Análise Dinâmica (DAST)
- Testes de penetração regulares
- Scanners automáticos em staging
- Revisão manual para changes críticas

### Segurança em Código
- Input validation em toda entrada
- Prepared statements para SQL
- Escape output para XSS
- Rate limiting em endpoints públicos
- CSRF tokens em mutações
- Content Security Policy headers
- Helmet.js (Express) para headers HTTP

---

## Documentação

### Onde Documentar
| Conteúdo | Local |
|----------|-------|
| Setup do projeto | README.md |
| Convenções de código | AGENTS.md / CONTRIBUTING.md |
| Decisões arquiteturais | ADR (docs/adr/) |
| API | Swagger/OpenAPI |
| Arquitetura | docs/architecture.md |
| Runbook | docs/runbook.md |

### Checklist de Documentação
- [ ] README com setup, scripts, dependências
- [ ] AGENTS.md com convenções do projeto
- [ ] API documentada (Swagger)
- [ ] ADRs para decisões arquiteturais
- [ ] CHANGELOG atualizado
- [ ] Comentários de código apenas para lógica não óbvia

---

## Checklist do Engenheiro

### Antes de Codificar
- [ ] Li README e AGENTS.md do projeto
- [ ] Entendi a estrutura de diretórios
- [ ] Verifiquei dependências existentes
- [ ] Identifiquei código similar como referência
- [ ] Plano claro do que será alterado

### Durante a Codificação
- [ ] Uma alteração lógica por commit
- [ ] Convenções de nomenclatura seguidas
- [ ] Imports organizados
- [ ] Sem placeholders ou TODOs
- [ ] Tratamento de erros adequado

### Antes de Commitar
- [ ] `git diff` revisado (sem segredos)
- [ ] `git status` verificado (arquivos corretos)
- [ ] Lint passando
- [ ] Typecheck passando
- [ ] Testes passando
- [ ] Build bem-sucedido

### Antes do PR
- [ ] Branch atualizada com alvo (rebase)
- [ ] Commits organizados e descritivos
- [ ] Testes para novas funcionalidades
- [ ] Documentação atualizada
- [ ] CI passando
- [ ] CHANGELOG atualizado
