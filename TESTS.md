# Skill: Testes

## Objetivo
Orientar agentes de IA na escrita, execução e manutenção de testes de software seguindo as melhores práticas e conventions do projeto.

## Quando usar
- Ao implementar uma nova funcionalidade
- Ao corrigir um bug
- Ao refatorar código existente
- Ao melhorar cobertura de testes
- Ao investigar falhas em CI

## Pirâmide de Testes

```
        ╱ ╲
       ╱ e2e ╲
      ╱───────╲
     ╱         ╲
    ╱integração ╲
   ╱─────────────╲
  ╱               ╲
 ╱  unitários      ╲
╱═══════════════════╲
```

| Tipo | Velocidade | Cobertura | Manutenção | Onde usar |
|------|-----------|-----------|------------|-----------|
| Unitário | ⚡ Rápido | Pequena | Baixa | Lógica pura, utils, hooks |
| Integração | 🐢 Médio | Média | Média | APIs, banco, serviços |
| E2E | 🐌 Lento | Alta | Alta | Fluxos críticos do usuário |

## Convenções

### Nomenclatura

```typescript
// Arquivo de teste deve espelhar o fonte
// fonte: src/services/auth.ts
// teste: src/services/auth.test.ts ou src/services/__tests__/auth.test.ts

// Nome do teste: descreva o comportamento, não a implementação
describe('AuthService', () => {
  it('deve retornar token JWT quando credenciais forem válidas', () => { ... })
  it('deve lançar UnauthorizedError quando senha estiver incorreta', () => { ... })
  it('deve bloquear após 5 tentativas consecutivas', () => { ... })
})
```

### Padrão Triple A (Arrange-Act-Assert)

```typescript
it('deve calcular desconto para cliente VIP', () => {
  // Arrange
  const cliente = criarClienteVIP()
  const pedido = new Pedido(100, cliente)

  // Act
  const total = pedido.calcularTotal()

  // Assert
  expect(total).toBe(90) // 10% de desconto
})
```

## Estratégia de Testes

### O que testar

✅ **Prioridade Alta**
- Lógica de negócio (regras, cálculos, validações)
- Casos de borda (zero, negativo, nulo, vazio, duplicado)
- Tratamento de erros (exceções, falhas de rede, timeouts)
- Contratos de API (status codes, formato de resposta)

⚠️ **Prioridade Média**
- Integração com banco de dados
- Integração com serviços externos (mocados)
- Testes de snapshot para componentes estáveis

❌ **Evitar**
- Testar frameworks ou bibliotecas (React, Express, Prisma)
- Testar getters/setters triviais
- Testar implementação em vez de comportamento
- Testar código que será removido em breve

### Cobertura Mínima

| Métrica | Alvo | Aceitável |
|---------|------|-----------|
| Linhas | 85% | 75% |
| Branches | 80% | 70% |
| Funções | 90% | 80% |

## Mocking

### Regras de Ouro

1. **Mock apenas o que é seu** — nunca mock bibliotecas externas sem necessidade
2. **Mock borders do sistema** — I/O, rede, sistema de arquivos, tempo
3. **Prefira stubs a mocks** — stub retorna dado; mock verifica interação
4. **Evite mocks deep** — prefira injeção de dependência

```typescript
// ✅ Bom: mock na fronteira do sistema
const emailService = { send: vi.fn().mockResolvedValue(true) }
const auth = new AuthService(emailService)

// ❌ Ruim: mock de biblioteca externa sem necessidade
vi.mock('bcrypt')
vi.mock('jsonwebtoken')
```

## Testes Específicos por Camada

### Frontend (React/Vue)

```typescript
// Teste de componente
it('deve exibir erro quando formulário for submetido vazio', () => {
  render(<LoginForm />)
  fireEvent.click(screen.getByRole('button', { name: 'Entrar' }))
  expect(screen.getByText('Campo obrigatório')).toBeInTheDocument()
})

// Teste de hook
it('deve retornar usuário após login bem-sucedido', async () => {
  const { result } = renderHook(() => useAuth())
  await act(async () => {
    await result.current.login('email@test.com', 'senha123')
  })
  expect(result.current.user).toBeDefined()
})
```

### Backend (Node.js)

```typescript
// Teste de controller
it('deve retornar 201 ao criar usuário', async () => {
  const response = await request(app)
    .post('/api/users')
    .send({ name: 'João', email: 'joao@test.com' })
  expect(response.status).toBe(201)
  expect(response.body).toHaveProperty('id')
})

// Teste de serviço
it('deve lançar erro se email já existir', async () => {
  await usuarioRepository.criar({ email: 'existente@test.com' })
  await expect(
    usuarioService.criar({ email: 'existente@test.com' })
  ).rejects.toThrow(EmailJaExisteError)
})
```

### Testes de Integração

```typescript
it('deve persistir usuário e retornar token', async () => {
  // Database real ou testcontainers
  const db = await crearBancoTeste()
  const auth = new AuthService(db)

  const resultado = await auth.registrar({
    email: 'teste@test.com',
    senha: 'Str0ng!Pass'
  })

  const usuario = await db.usuario.findByEmail('teste@test.com')
  expect(usuario).toBeDefined()
  expect(resultado.token).toBeDefined()
})
```

## Comandos por Framework

```bash
# Vitest
npx vitest                       # roda todos
npx vitest --run                 # roda uma vez
npx vitest src/auth.test.ts      # arquivo específico
npx vitest --coverage            # com cobertura
npx vitest --watch               # modo watch

# Jest
npx jest                         # roda todos
npx jest src/auth.test.ts        # arquivo específico
npx jest --coverage              # com cobertura
npx jest --watch                 # modo watch

# Pytest
pytest                           # roda todos
pytest tests/test_auth.py        # arquivo específico
pytest -k "login"                # filtra por nome
pytest --cov=src                 # cobertura
pytest --cov-report=html         # relatório HTML

# Go
go test ./...                    # roda todos
go test ./internal/auth/         # pacote específico
go test -v ./...                 # verbose
go test -run TestLogin           # teste específico
go test -coverprofile=coverage.out

# Rust
cargo test                       # roda todos
cargo test login                 # filtra por nome
cargo test -- --nocapture        # mostra stdout
cargo tarpaulin --ignore-tests   # cobertura
```

## Dicas Avançadas

### Testando Erros Assíncronos

```typescript
it('deve rejeitar promise com erro específico', async () => {
  await expect(
    service.processar(dadoInvalido)
  ).rejects.toThrow(ValidationError)
})
```

### Snapshots (use com moderação)

```typescript
it('deve renderizar componente corretamente', () => {
  const { container } = render(<MeuComponente />)
  expect(container).toMatchSnapshot()
})
```

### Test Parameterizado

```typescript
it.each([
  { input: 0, expected: 0 },
  { input: -1, expected: 0 },
  { input: 100, expected: 10 },
  { input: 1000, expected: 50 },
])('deve calcular imposto para $input', ({ input, expected }) => {
  expect(calcularImposto(input)).toBe(expected)
})
```

## Checklist Antes de Submeter

- [ ] Todos os testes passam localmente
- [ ] Cobertura mínima foi atingida
- [ ] Testes para casos de borda incluídos
- [ ] Testes de regressão para bugs corrigidos
- [ ] Nenhum teste comentado ou com `.skip`
- [ ] Mocks foram resetados entre testes
- [ ] Testes são determinísticos (mesmo resultado sempre)
- [ ] Testes não dependem de ordem de execução
