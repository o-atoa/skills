# TESTS — Testes de Software

## Objetivo
Orientar agentes de IA na escrita, execução e manutenção de testes de software seguindo melhores práticas e convenções do projeto.

## Quando usar
- Ao implementar nova funcionalidade, corrigir bug, refatorar, melhorar cobertura, investigar falhas em CI

## Pirâmide de Testes

```
         ╱ ╲
        ╱ e2e ╲
       ╱───────╲
      ╱integração╲
     ╱─────────────╲
    ╱  unitários    ╲
   ╱═════════════════╲
```

| Tipo | Velocidade | Cobertura | Manutenção | Onde usar |
|------|-----------|-----------|------------|-----------|
| Unitário | Rápido | Pequena | Baixa | Lógica pura, utils, hooks |
| Integração | Médio | Média | Média | APIs, banco, serviços |
| E2E | Lento | Alta | Alta | Fluxos críticos do usuário |

## Convenções

```typescript
// Arquivo espelha fonte: src/services/auth.ts → src/services/auth.test.ts
describe('AuthService', () => {
  it('deve retornar token JWT quando credenciais forem válidas', () => {})
  it('deve lançar UnauthorizedError quando senha estiver incorreta', () => {})
  it('deve bloquear após 5 tentativas consecutivas', () => {})
})
```

### Triple A (Arrange-Act-Assert)
```typescript
it('deve calcular desconto VIP', () => {
  const cliente = criarClienteVIP(); const pedido = new Pedido(100, cliente)
  const total = pedido.calcularTotal()
  expect(total).toBe(90)
})
```

## O que testar

**Alta**: lógica de negócio, bordas, erros, contratos
**Média**: integração DB, serviços externos, snapshots
**Evitar**: frameworks, getters/setters, implementação

| Cobertura | Alvo | Aceitável |
|-----------|------|-----------|
| Linhas | 85% | 75% |
| Branches | 80% | 70% |
| Funções | 90% | 80% |

## Mocking

1. Mock apenas o que é seu
2. Mock borders do sistema (I/O, rede, FS, tempo)
3. Prefira stubs a mocks
4. Evite mocks deep — prefira injeção de dependência

```typescript
// Mock na fronteira
const emailService = { send: vi.fn().mockResolvedValue(true) }
const auth = new AuthService(emailService)
// Mock de lib externa (evitar)
vi.mock('bcrypt')
```

## Tipos Avançados de Teste

### Property-Based Testing (QuickCheck/Hypothesis)
Define propriedades invariantes; framework gera entradas.
```typescript
test('ordenação é idempotente', () => {
  fc.assert(fc.property(fc.array(fc.integer()), (arr) => {
    expect(ordenar(ordenar(arr))).toEqual(ordenar(arr))
  }))
})
```
Ideal para funções puras, algoritmos, parsers, validações.

### Mutation Testing (Stryker)
Introduz mutações no código — testes devem detectá-las. Mutation score > 80% é ideal. Se teste passa com código mutado, não está testando direito.
```bash
npx stryker run
```

### Contract Testing (Pact)
Consumer-driven contracts entre microsserviços. Garante que provedor e consumidor evoluem em acordo sem rodar ecossistema inteiro.

### Snapshot Testing
```typescript
expect(container).toMatchSnapshot()
```
**Usar**: componentes estáveis, dados previsíveis. **Evitar**: componentes voláteis, CSS-in-JS com hash.

### Visual Regression (Percy, Chromatic)
Compara screenshots. Ideal para design systems e temas.

### Fuzz Testing
Entradas aleatórias/malformadas para achar crashes. Indispensável para parsers e validadores.
```python
@given(st.text())
def test_sanitizacao_nao_quebra(s):
    assert isinstance(sanitizar(s), str)
```

### Chaos Testing
Injeta falhas (derrubar serviços, latência) para validar resiliência. Sistemas críticos com SLOs.

### Load/Stress Testing
```javascript
export const options = { vus: 100, duration: '30s' }
```
**Load**: carga esperada. **Stress**: ponto de ruptura. **Spike**: picos súbitos. **Soak**: vazamento de memória.

### Golden File / Approval Tests
Compara saída completa contra arquivo aprovado. Para parsers, relatórios, transformações complexas.

## Estratégias por Arquitetura

#### Microsserviços
Unitário → lógica pura | Contract → Pact | Integration → Testcontainers | E2E → 1-2 fluxos | Chaos → resiliência

### Hexagonal (Ports & Adapters)
Domínio nunca depende de infra — teste com implementações in-memory das portas, sem mock framework.
```typescript
class InMemoryUserRepository implements UserRepository {
  private users = new Map<string, User>()
  async findById(id: string) { return this.users.get(id) ?? null }
}
it('deve processar domínio sem I/O', () => {
  const service = new UserService(new InMemoryUserRepository())
  // testa lógica pura
})
```

### Event-Driven
- Teste produtores: evento correto foi emitido
- Teste consumidores: idempotência (mesmo evento duas vezes = mesmo resultado)
- Teste ordenação: eventos fora de ordem
- Use in-memory bus em unitários, broker real em integração

### Banco de Dados
- Testcontainers para isolamento
- Transação com rollback ao final
- Factory pattern para dados de setup
- Teste migrações (up/down)
- Nunca use banco compartilhado

### State Machine Testing
Teste todas as transições válidas e inválidas da máquina de estados.

## Design de Teste

### Equivalence Partitioning
Domínio dividido em partições de comportamento equivalente; teste um valor de cada.
```typescript
it.each([{ idade: 5, esperado: 'crianca' }, { idade: 15, esperado: 'adolescente' }, { idade: 30, esperado: 'adulto' }])(
  'classifica $idade como $esperado', ({ idade, esperado }) => expect(classificar(idade)).toBe(esperado))
```

### Boundary Value Analysis
Maioria dos bugs está nas fronteiras.
```typescript
it.each([0, 12, 13, 17, 18, 120])('limite $idade', (idade) => {
  expect(() => classificar(idade)).not.toThrow()
})
```

### Decision Table
Para lógica com múltiplas condições (2ⁿ combinações).

| VIP | BlackFri | Cupom | Desc. |
|-----|----------|-------|-------|
| F | F | F | 0% |
| F | V | V | 30% |
| V | F | F | 15% |
| V | V | V | 40% |

### Outras Técnicas
- **State Transition**: cubra todas as transições do diagrama
- **Pairwise**: reduz combinações para pares (`pict`, `allpairspy`)
- **Classification Tree**: entradas hierárquicas
- **Use Case**: fluxo básico + alternativos + exceção

## Manutenibilidade de Testes

### Test Code é Production Code
Mesmo padrão: lint, typecheck, review, sem duplicação.

### DRY vs DAMP
- **DRY** no setup/helpers | **DAMP** no corpo do teste (legibilidade > abstração)

```typescript
// DAMP: claro o que testa
it('deve negar pagamento com cartão vencido', () => {
  expect(() => processar(new Cartao('1234', mesPassado, anoPassado, '123')))
    .toThrow('Cartão vencido')
})
```

### Factories e Builders
```typescript
function buildUser(overrides: Partial<User> = {}) {
  return { id: faker.string.uuid(), name: 'João', email: 'joao@test.com', role: 'user', ...overrides }
}
class UserBuilder {
  private u = buildUser()
  withRole(r: Role) { this.u.role = r; return this }
  build() { return this.u }
}
```

### Shared Infrastructure
Helpers em `tests/helpers/`, setup global em `tests/setup.ts`, reset mocks no `afterEach`. Evite herança.

### Evitando Interdependência
- Cada teste roda isolado
- Nunca compartilhe estado mutável
- `--shard` e `--bail` devem funcionar sem quebrar

### Flaky Tests

| Causa | Solução |
|-------|---------|
| Race condition | `waitFor`, retry |
| Ordem de execução | Isole estado |
| Timing | Evite setTimeout fixo |
| Dado compartilhado | Factory com dados únicos |
| Mock não resetado | `vi.clearAllMocks()` no afterEach |

Detecte com `--repeat=50`.

### Categorização
| Categoria | Descrição | Execução |
|-----------|-----------|----------|
| Smoke | Build, health check | Pré-deploy |
| Regression | Nada quebrou | CI |
| Acceptance | Critérios de aceite | PR + staging |

```typescript
it('health check', { tags: ['smoke'] }, () => {})
```

## Métricas de Cobertura

### Line vs Branch vs Path
- **Line**: executou cada linha? (mais fraca)
- **Branch**: cada decisão true/false? (mínima aceitável)
- **Path**: cada caminho único? (exponencial, impraticável)

### Mutation Score
Cobertura de linha mostra que código rodou. Mutation score mostra que teste **detectou** mudança. Score < 70% = testes superficiais.

### Goodhart's Law
> "Quando métrica vira alvo, deixa de ser boa métrica."
Cobertura é sinal, não target contratual. Times que miram 100% escrevem testes superficiais.

### Coverage-Informed Prioritization
```bash
npx vitest --changed
pytest --cov --diff
```
Execute apenas testes que cobrem código alterado. Ferramentas: `jest --onlyChanged`, `pytest-testmon`.

## TDD (Test-Driven Development)

### Red-Green-Refactor
```
RED:   escreva teste que falhe
GREEN: código mínimo para passar
REFACTOR: melhore mantendo testes verdes
```

### Regras
1. Escreva só o teste suficiente para falhar
2. Escreva só o código suficiente para passar
3. Refatore apenas com testes verdes

### London vs Detroit

| Escola | Abordagem | Mocks |
|--------|-----------|-------|
| **London** (outside-in) | Mock colaboradores, uma classe por vez | Pesado |
| **Detroit** (classicist) | Teste estado, integre objetos reais | Mínimo |

London: arquitetura hexagonal, CQRS. Detroit: domínio rico, algoritmos.

### Quando TDD funciona
**Adequado**: Regras de negócio, algoritmos, validações, parsers, APIs REST
**Inadequado**: UI exploratória, prototipação, código legado sem testes

## BDD e Testes de Aceitação

### Given-When-Then
```gherkin
Funcionalidade: Desconto VIP
  Cenário: Cliente VIP compra acima de R$ 100
    Dado um cliente VIP
    E um pedido de R$ 200
    Quando o total é calculado
    Então o desconto deve ser de 15%
```

### Living Documentation
Features Gherkin viram documentação viva, executável, legível por não-técnicos. Ferramentas: Cucumber, Serenity BDD.

### Example Mapping
Discovery com cartões: amarelo (história), azul (regra), verde (exemplo), vermelho (pergunta). Organiza conversa PO-dev-QA antes de escrever cenários.

## Testes por Camada

### Frontend
```typescript
it('deve exibir erro em formulário vazio', () => {
  render(<LoginForm />)
  fireEvent.click(screen.getByRole('button', { name: 'Entrar' }))
  expect(screen.getByText('Campo obrigatório')).toBeInTheDocument()
})
it('deve retornar usuário após login', async () => {
  const { result } = renderHook(() => useAuth())
  await act(async () => { await result.current.login('email@test.com', 'senha123') })
  expect(result.current.user).toBeDefined()
})
```

### Backend
```typescript
it('deve retornar 201 ao criar usuário', async () => {
  const response = await request(app).post('/api/users').send({ name: 'João' })
  expect(response.status).toBe(201)
  expect(response.body).toHaveProperty('id')
})
it('deve lançar erro se email existir', async () => {
  await usuarioRepository.criar({ email: 'existente@test.com' })
  await expect(usuarioService.criar({ email: 'existente@test.com' })).rejects.toThrow(EmailJaExisteError)
})
```

### Integração
```typescript
it('deve persistir usuário e retornar token', async () => {
  const db = await criarBancoTeste()
  const resultado = await new AuthService(db).registrar({ email: 'teste@test.com', senha: 'Str0ng!Pass' })
  expect(await db.usuario.findByEmail('teste@test.com')).toBeDefined()
  expect(resultado.token).toBeDefined()
})
```

## Testes em CI/CD

### Paralelização
```bash
npx vitest --shard=1/4 && pytest -n auto
```
Balanceie por duração histórica, não por número de arquivos.

### Test Selection
Execute apenas testes afetados pelo diff do PR.
```bash
npx vitest --changed && npx jest -o && pytest-testmon
```
`vitest --related`, Bazel, Nx, Turborepo para impact analysis além do diff.

### Quarantine
Teste flaky não bloqueia CI — move para quarentena até correção.
```typescript
it('processa pagamento', { retry: 3, tags: ['quarantine'] }, () => {})
```
Fluxo: detectar → quarentena → investigar → corrigir → remover.

### Reporting
```bash
npx vitest --reporter=junit --outputFile=test-results.xml
```
JUnit para CI, HTML para visualização, analytics para regressão de performance.

## Comandos por Framework

```bash
# Vitest
npx vitest {--run, --coverage, --changed, --shard=1/4}  # roda / coverage / afetados / paralelo
npx vitest src/auth.test.ts                             # específico

# Jest
npx jest {,-o,--coverage}                               # todos / onlyChanged / coverage

# Pytest
pytest {,-k "login",-n auto}                            # todos / filtro / paralelo
pytest --cov=src --cov-report=html                      # cobertura

# Go
go test {./...,-v -run TestLogin}                       # todos / específico
go test -coverprofile=coverage.out

# Rust
cargo test {,login,-- --nocapture}                      # todos / filtro / stdout
cargo tarpaulin --ignore-tests                          # cobertura
```

## Dicas Avançadas

```typescript
// Erro assíncrono
await expect(service.processar(dadoInvalido)).rejects.toThrow(ValidationError)

// Snapshot
expect(container).toMatchSnapshot()

// Parameterizado
it.each([0, -1, 100])('imposto para $input', (input) => {
  expect(calcularImposto(input)).toBeGreaterThanOrEqual(0)
})

// Fake timers
it('deve expirar sessão após 30min', () => {
  vi.useFakeTimers()
  const sessao = new Sessao()
  vi.advanceTimersByTime(31 * 60 * 1000)
  expect(sessao.expirada()).toBe(true)
  vi.useRealTimers()
})
```

## Checklist Antes de Submeter

- [ ] Todos os testes passam localmente
- [ ] Cobertura mínima atingida
- [ ] Casos de borda incluídos
- [ ] Testes de regressão para bugs corrigidos
- [ ] Nenhum teste comentado ou com `.skip`
- [ ] Mocks resetados entre testes
- [ ] Testes determinísticos (mesmo resultado sempre)
- [ ] Testes não dependem de ordem de execução
- [ ] Nenhum teste flaky conhecido na suite
- [ ] Mutation score > 70% (quando aplicável)
