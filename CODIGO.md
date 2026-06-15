# CÓDIGO — Estilo e Convensões de Código

> Guia completo de estilo de código, documentação e boas práticas de programação. Baseado em convenções oficiais de linguagens (PEP 8, Google Style, StandardJS, AirBnB) e práticas de mercado.

---

## Princípios Gerais

### Filosofia
- **Legibilidade** sobre performance (a menos que comprovadamente crítico)
- **Simplicidade** sobre genéricidade (YAGNI — You Ain't Gonna Need It)
- **Consistência** sobre preferência pessoal (siga o estilo do projeto)
- **DRY** mas não a qualquer custo — duplicação acidental > abstração prematura
- **Código executável** imediatamente — sem placeholders, TODOs ou `// ...`

### Hierarquia de Decisões de Estilo
1. Convenções explícitas do projeto (AGENTS.md, eslint, prettier, rustfmt, gofmt)
2. Convenções do framework/biblioteca utilizado
3. Convenções da linguagem (PEP 8, StandardJS, etc.)
4. Este guia
5. Bom senso

---

## Nomenclatura

### Padrões por Linguagem

| Convenção | Onde usar | Exemplos |
|-----------|-----------|----------|
| `camelCase` | JavaScript/TypeScript: variáveis, funções, métodos | `getUserById`, `userName` |
| `PascalCase` | Classes, componentes React, types, interfaces, enums | `UserService`, `LoginForm` |
| `snake_case` | Python: variáveis, funções; Rust: variáveis; SQL | `get_user_by_id`, `user_name` |
| `SCREAMING_SNAKE` | Constantes globais, enums values | `MAX_RETRY_COUNT`, `ERROR_NOT_FOUND` |
| `kebab-case` | Arquivos, diretórios, URLs, CSS classes | `user-service.ts`, `login-form.tsx` |
| `UPPER_CASE` | Nomes de arquivos de configuração | `.env`, `Dockerfile`, `Makefile` |

### Preferências Gerais
- Nomes descritivos: `calculateTotalPrice()` e não `calc()`
- Abreviações apenas quando universais: `id`, `url`, `html`, `api`
- Evite abreviações criativas: `usr` → `user`, `btn` → `button`
- Nomes booleanos: prefixo com `is`, `has`, `can`, `should`
- Nomes de coleções: plural (`users`, `items`, `configs`)
- Evite redunância: `user.userName` e não `user.userNameValue`

---

## Estrutura de Arquivos

### Organização de Projetos
```
projeto/
├── src/                  # Código fonte
│   ├── components/       # Componentes React/Vue
│   ├── services/         # Lógica de negócio
│   ├── utils/            # Utilitários puros
│   ├── hooks/            # Custom hooks
│   ├── types/            # Types/Interfaces/Models
│   └── __tests__/        # Testes
├── public/               # Assets estáticos
├── docs/                 # Documentação
└── scripts/              # Scripts de automação
```

### Responsabilidade por Arquivo
- Um arquivo = uma responsabilidade principal
- Máximo 200-400 linhas por arquivo (acima disso, extraia)
- Componentes React: um componente por arquivo
- Serviços: agrupe por domínio (`auth.service.ts`, `user.service.ts`)
- Testes: espelhe a estrutura do fonte (`auth.service.test.ts`)

### Imports
1. **Ordem**: externos → internos → relativos
2. **Externos**: bibliotecas npm/pip/cargo
3. **Internos**: módulos do projeto (`@/services/auth`)
4. **Relativos**: arquivos vizinhos (`./utils/format`)
5. Separe grupos com linha em branco
6. Prefira imports absolutos (com alias) sobre relativos profundos

```typescript
// Boa prática
import { z } from 'zod'
import { clsx } from 'clsx'

import { authService } from '@/services/auth'
import { formatDate } from '@/utils/date'

import { UserCard } from './user-card'
import { useAuth } from './use-auth'

// Evitar
import { formatDate } from '../../../../utils/date'
```

---

## Padrões por Tipo

### Funções
- **Puras** preferencialmente — sem efeitos colaterais quando possível
- **Pequenas** — uma função, uma responsabilidade (SRP)
- **Parâmetros**: máximo 3; use objeto de configuração para mais
- **Retorno** explícito e tipado
- **Async**: sempre usar async/await, não callbacks ou .then()

```typescript
// Boa prática
interface SearchParams {
  query: string
  limit?: number
  offset?: number
}

async function searchUsers(params: SearchParams): Promise<User[]> {
  const { query, limit = 10, offset = 0 } = params
  return db.user.findMany({ where: { name: query }, take: limit, skip: offset })
}

// Evitar
function searchUsers(query: string, limit?: number, offset?: number, sortBy?: string, order?: string) {
  // mais de 3 parâmetros
}
```

### Classes (quando necessário)
- Prefira funções e módulos sobre classes (JS/TS)
- Use classes apenas para: Domain Models, Value Objects, ou quando polimorfismo é necessário
- Nomes no singular: `User`, `Order`, `PaymentProcessor`

### Testes
```typescript
describe('AuthService', () => {
  it('deve retornar token JWT quando credenciais forem válidas', async () => {
    // Arrange
    const credentials = { email: 'test@test.com', password: 'Str0ng!Pass' }
    const service = new AuthService()

    // Act
    const result = await service.login(credentials)

    // Assert
    expect(result.token).toBeDefined()
    expect(result.user.email).toBe(credentials.email)
  })
})
```

---

## Tratamento de Erros

### Padrão Geral
```typescript
// Tratamento adequado
async function getUser(id: string): Promise<User> {
  try {
    const user = await db.user.findUnique({ where: { id } })
    if (!user) throw new NotFoundError(`User ${id} not found`)
    return user
  } catch (error) {
    if (error instanceof NotFoundError) throw error
    throw new InternalError('Failed to fetch user', { cause: error })
  }
}
```

### Regras
- Nunca coloque try/catch em torno de imports
- Use error boundaries em React em vez de try/catch em componentes
- Erros devem ser categorizados: validação, autenticação, não encontrado, interno
- Mensagens de erro em português ou inglês (consistente com o projeto)
- Nunca exponha stack traces ou detalhes internos ao usuário
- Faça fallback graciosamente, não quebre a aplicação

---

## TypeScript

### Strict Mode
- `strict: true` no tsconfig.json
- Evite `any` — prefira `unknown` + type guard
- Use `as const` para literais
- Prefira `interface` para objetos públicos, `type` para uniões

```typescript
// Type-first
type UserStatus = 'active' | 'inactive' | 'suspended'

interface User {
  id: string
  email: string
  status: UserStatus
  createdAt: Date
}

// Evitar
const user: any = getUser()
```

---

## Segurança em Código

### Nunca
- Hardcode secrets, API keys, tokens ou senhas
- Expor dados sensíveis em logs, console.log ou mensagens de erro
- Usar `eval()`, `Function()`, ou `setTimeout(string)` (injeção)
- Confiar em input do usuário sem validação
- Construir SQL por concatenação de strings

### Sempre
- Validar input em todas as camadas (frontend, API, serviço)
- Usar ORM/query builder parametrizado
- Escapar output para XSS
- Aplicar rate limiting em endpoints públicos
- Usar HTTPS em produção

---

## Performance

### Regras Gerais
1. **Meça antes de otimizar** — não otimize prematuramente
2. **Consultas N+1** — sempre suspecte e previna (use include/join/eager loading)
3. **Memoização** — para cálculos pesados e referências estáveis
4. **Lazy loading** — carregue sob demanda, não tudo de uma vez
5. **Cache** — resultados de consultas frequentes e estáveis
6. **Bundle size** — tree-shaking, code splitting, lazy routes

---

## AGENTS.md (Instruções de Projeto)

Arquivos AGENTS.md contêm instruções específicas do projeto:
- Localização: qualquer diretório no projeto
- Escopo: árvore de diretórios abaixo dele
- Precedência: mais aninhado > menos aninhado > este guia
- Instruções diretas do usuário > qualquer AGENTS.md

Sempre verifique AGENTS.md no diretório relevante antes de começar a codificar.

---

## Checklist de Qualidade de Código

- [ ] Código segue as convenções de nomenclatura do projeto
- [ ] Funções têm responsabilidade única
- [ ] Nomes são descritivos e auto-documentáveis
- [ ] Imports estão organizados e limpos
- [ ] Sem imports ou variáveis não utilizadas
- [ ] Tratamento de erros adequado (não apenas try/catch genérico)
- [ ] Tipos estão corretos (sem uso de `any` ou casts desnecessários)
- [ ] Testes cobrem casos principais e de borda
- [ ] Documentação de funções públicas (JSDoc/TSDoc quando aplicável)
- [ ] Sem segredos, credenciais ou dados sensíveis
- [ ] Código é executável imediatamente (imports incluídos)
- [ ] Lint passando (eslint, ruff, etc.)
- [ ] Typecheck passando (tsc, mypy, etc.)
