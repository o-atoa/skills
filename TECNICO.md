# TÉCNICO — Precisão Técnica e Design de Sistemas

> Guia completo de precisão técnica, design de sistemas, algoritmos, APIs, performance e engenharia de software. Baseado em princípios de ciência da computação, padrões de design e melhores práticas da indústria.

---

## 1. Precisão em Respostas Técnicas

### 1.1 Diretrizes Gerais
- **Nunca** use meta-frases ("deixe-me ajudar", "posso ver que", "ótima pergunta").
- **Seja específico**: troque "considere usar caching" por "adicione Redis com TTL de 300s para a query X".
- **Quantifique sempre**: não "melhora performance", mas "reduz latência p95 de 2s para 200ms".
- **Prefira código executável** a pseudocódigo. Se usar pseudocódigo, seja rigoroso.
- **Cada afirmação técnica** deve ser justificável com: evidência empírica, documentação oficial, ou primeiro princípio.
- **Reconheça incerteza** com intervalos de confiança, não com "talvez".

### 1.2 Estrutura de Resposta Técnica
```
Problema: [descrição concisa]

Causa Raiz: [1-2 frases]

Solução: [código ou comando direto]

Impacto: [métricas antes/depois]

Trade-offs: [o que se perde com esta solução]
```

### 1.3 Armadilhas de Precisão
| Armadilha | Exemplo | Correção |
|-----------|---------|----------|
| **Generalização excessiva** | "Sempre use async/await" | "Use async/await para I/O-bound; prefira workers para CPU-bound" |
| **Falsa dicotomia** | "SQL vs NoSQL" | "SQL para dados relacionais, NoSQL para documentos/grafos" |
| **Argumento de autoridade** | "Google usa, então deve ser bom" | "Google usa porque escala para X; seu caso tem Y requisitos diferentes" |
| **Viés de recência** | "React 19 é sempre melhor" | "Avalie: maturidade, ecossistema, equipe, requisitos" |

---

## 2. Algoritmos e Estruturas de Dados

### 2.1 Complexidade Algorítmica

| Notação | Nome | Exemplo | Escalabilidade |
|---------|------|---------|----------------|
| O(1) | Constante | Array lookup, hash table get | Instantâneo |
| O(log n) | Logarítmico | Binary search, balanced tree | Excelente |
| O(n) | Linear | Array scan, linked list walk | Bom |
| O(n log n) | Quase linear | Merge/Quick sort, heap ops | Adequado |
| O(n²) | Quadrático | Nested loops, bubble sort | Ruim para n > 1000 |
| O(2ⁿ) | Exponencial | Subset enumeration, brute force | Evitar para n > 20 |
| O(n!) | Fatorial | Permutation generation | Inviável para n > 10 |

**Dica**: meça antes de otimizar. Perfil é melhor que intuição.

### 2.2 Padrões Algorítmicos

| Padrão | Quando usar | Exemplo |
|--------|-------------|---------|
| **Two pointers** | Array ordenado, palíndromo | Two Sum, container with most water |
| **Sliding window** | Subarray/substring contínuo | Max sum subarray, longest substring |
| **Divide & conquer** | Problema divisível em subproblemas | Merge sort, quick sort, binary search |
| **Greedy** | Escolha ótima local leva a global | Dijkstra, Huffman, coin change (alguns casos) |
| **Dynamic programming** | Subproblemas sobrepostos, ótimo local | Fibonacci, knapsack, LCS, LIS |
| **Backtracking** | Explorar todas as possibilidades | N-queens, sudoku, permutation |
| **Graph traversal (DFS/BFS)** | Relacionamentos, caminhos, conectividade | Shortest path, topological sort |
| **Binary search** | Dado monotônico, busca por valor | Find in rotated array, sqrt |
| **Union-Find** | Conectividade dinâmica, componentes | Kruskal's MST, rede social |
| **Trie** | Prefix matching, autocomplete | Dictionary, spell checker |

### 2.3 Estruturas de Dados por Caso de Uso

| Necessidade | Estrutura | Operações |
|-------------|-----------|-----------|
| Lookup por chave | Hash Table | O(1) insert, delete, get |
| Dados ordenados | Balanced BST (TreeMap) | O(log n) insert, delete, range query |
| FIFO | Queue (array ou linked list) | O(1) enqueue, dequeue |
| LIFO | Stack | O(1) push, pop |
| Fila prioritária | Heap (PriorityQueue) | O(log n) push, pop min/max |
| Cache LRU | LinkedHashMap | O(1) get, put |
| Grafos esparsos | Adjacency list | O(V + E) traversal |
| Strings | Trie / Suffix Array | O(k) lookup, O(n) construção |

---

## 3. Design de Sistemas

### 3.1 Framework de Design
Sempre avalie estas dimensões na ordem:

1. **Requisitos**: funcionais + não-funcionais (latência, throughput, consistência)
2. **Estimativas**: QPS, armazenamento, banda, cache hit ratio
3. **Modelo de dados**: entidades, relacionamentos, schema
4. **API/Contratos**: interface entre componentes
5. **Componentes**: serviço, banco, cache, fila, worker, CDN
6. **Fluxos**: request path, fallbacks, retries
7. **Trade-offs**: consistência vs disponibilidade, latência vs throughput, custo vs performance

### 3.2 Cálculos de Estimativa Rápida

| Operação | Ordem de grandeza |
|----------|-------------------|
| Request por segundo (RPS) | 1M diário ≈ 12/s, 10M ≈ 120/s |
| Leitura de SSD | ~500MB/s |
| Network intra-DC | ~1-10 Gbps |
| Redis single core | ~100k ops/s |
| CPU cycle | ~0.3ns |
| RAM access | ~100ns |
| Disk seek | ~10ms |
| Network round trip (DC) | ~0.5ms |
| Network (cross-region) | ~100ms |

### 3.3 Capacity Planning
```
Armazenamento/dia = QPS * tamanho_response * 86400
Armazenamento/ano = armazenamento/dia * 365
Banda = QPS * tamanho_response
Cache necessário = QPS * TTL * tamanho_response
Número de servidores = QPS / capacidade_por_servidor * fator_redundância
```

### 3.4 Estratégias de Cache

| Tipo | Latência | TTL | Uso |
|------|----------|-----|-----|
| **Browser cache** | 0 (local) | Anos | Assets estáticos, imagens |
| **CDN** | ~10ms | Minutos-horas | Conteúdo público, APIs |
| **In-memory (Redis/Memcached)** | <1ms | Segundos-minutos | Session, hot data |
| **Application cache (local)** | μs | Segundos | Cálculos repetidos, config |
| **Database cache** | ms | Variável | Query results, materialized views |

**Cache patterns**:
- **Cache aside**: app checa cache → miss → query DB → set cache → return
- **Read-through**: cache consulta DB automaticamente no miss
- **Write-through**: write no cache + DB síncrono
- **Write-behind**: write no cache → eventualmente no DB
- **Cache invalidation**: TTL, event-driven, manual (hard)

### 3.5 Padrões de Resiliência

| Padrão | Problema | Solução |
|--------|----------|---------|
| **Retry** | Falha transitória | Repetir com backoff exponencial + jitter |
| **Circuit Breaker** | Falha persistente | Abrir circuito após N falhas, testar após timeout |
| **Bulkhead** | Falha em cascata | Isolar recursos em pools separados |
| **Rate Limiting** | Sobrecarga | Token bucket, leaky bucket, sliding window |
| **Timeout** | Recurso lento | Limitar tempo de espera (connect, read, write) |
| **Fallback** | Serviço indisponível | Resposta padrão, degraded mode |
| **Health Check** | Instância morta | Probes (liveness, readiness, startup) |
| **Graceful Degradation** | Sobrecarga parcial | Desligar features não essenciais |

### 3.6 Estratégias de Consistência

| Estratégia | Garantia | Performance | Uso |
|------------|----------|-------------|-----|
| **Strong** | Sempre atual | Baixa | Transações financeiras |
| **Eventual** | Converge eventualmente | Alta | Social feeds, contadores |
| **Read-your-writes** | Lê seu próprio write | Média | Perfil do usuário |
| **Session** | Consistência na sessão | Média | Cesta de compras |
| **Monotonic reads** | Não ler dados antigos | Média | Timeline |

---

## 4. Design de APIs

### 4.1 RESTful API Design

#### Nomenclatura de Endpoints
```
✅ Boas práticas:
GET    /users                    → Listar usuários
GET    /users/:id                → Buscar usuário
POST   /users                    → Criar usuário
PUT    /users/:id                → Substituir usuário
PATCH  /users/:id                → Atualizar parcialmente
DELETE /users/:id                → Remover usuário
GET    /users/:id/orders         → Relacionamento (1:N)
GET    /users/:id/orders/:orderId → Relacionamento aninhado

❌ Evitar:
GET    /getUsers                 → Verbos na URL
POST   /updateUser               → Verbos na URL
DELETE /deleteUser?id=123        → Query params para identidade
GET    /users/listAll            → Ações como paths
```

#### Status Codes
| Código | Significado | Quando usar |
|--------|-------------|-------------|
| **200** | OK | GET, PUT, PATCH bem-sucedidos |
| **201** | Created | POST bem-sucedido |
| **204** | No Content | DELETE bem-sucedido |
| **301** | Moved Permanently | URL mudou permanentemente |
| **304** | Not Modified | Cache válido (ETag/If-Modified-Since) |
| **400** | Bad Request | Erro de validação, malformed |
| **401** | Unauthorized | Autenticação necessária/falhou |
| **403** | Forbidden | Autorização negada |
| **404** | Not Found | Recurso inexistente |
| **409** | Conflict | Conflito de estado (duplicata, stale) |
| **422** | Unprocessable Entity | Erro semântico de validação |
| **429** | Too Many Requests | Rate limit excedido |
| **500** | Internal Server Error | Erro inesperado no servidor |
| **502** | Bad Gateway | Upstream respondeu inválido |
| **503** | Service Unavailable | Sobrecarga ou manutenção |

#### Paginação
```json
// Cursor-based (recomendado para grandes volumes)
GET /users?cursor=abc123&limit=20
{
  "data": [...],
  "next_cursor": "def456",
  "has_more": true
}

// Page-based (simples, para volumes pequenos)
GET /users?page=1&per_page=20
{
  "data": [...],
  "total": 100,
  "page": 1,
  "per_page": 20,
  "total_pages": 5
}
```

#### Versionamento
- **URL**: `v1/users`, `v2/users` — simples, óbvio, mas polui URLs
- **Header**: `Accept: application/vnd.api+json;version=2` — limpo, mas esconde versão
- **Query**: `/users?version=2` — simples, mas caching problemático

### 4.2 GraphQL
```
// Query
query {
  user(id: "123") {
    name
    email
    posts(limit: 5) {
      title
      createdAt
    }
  }
}

// Mutation
mutation {
  createUser(input: { name: "João", email: "joao@test.com" }) {
    id
    name
  }
}
```

**Prós**: busca exata, forte tipagem, evita over/under-fetching.
**Contras**: caching complexo, query complexity analysis necessário, N+1 via resolvers.

### 4.3 gRPC
- **Proto**: contrato fortemente tipado
- **Transporte**: HTTP/2 (multiplexação, streaming bidirecional)
- **Payload**: Protocol Buffers (binário, compacto, rápido)
- **Geração**: código client/server automático
- **Uso**: microserviços, sistemas de alta performance, streaming

### 4.4 Error Response Pattern
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email inválido",
    "details": [
      {
        "field": "email",
        "reason": "Formato de email inválido",
        "code": "INVALID_FORMAT"
      }
    ],
    "request_id": "req_abc123",
    "timestamp": "2025-01-01T00:00:00Z"
  }
}
```

---

## 5. Performance

### 5.1 Metodologia de Otimização
```
1. PERFILAR (medir) — nunca otimize sem dados
2. IDENTIFICAR gargalo — CPU? I/O? Memória? Rede?
3. FORMULAR hipótese — "cache reduzirá latência em 50%"
4. IMPLEMENTAR mudança — mínima possível
5. MEDIR novamente — mesma métrica, mesmo cenário
6. REPETIR ou RETER — se melhorou, mantenha; senão, desfaça
```

### 5.2 Métricas-Chave
| Métrica | O que mede | Alerta em |
|---------|-----------|-----------|
| **Latência** (p50, p95, p99) | Tempo de resposta | p95 > 500ms (API), > 2s (página) |
| **Throughput** (RPS, TPS) | Capacidade de processamento | Queda > 20% |
| **Error rate** (4xx, 5xx) | Porcentagem de erros | > 1% (5xx), > 5% (4xx) |
| **CPU** | Utilização do processador | > 80% sustentado |
| **Memory** | Uso de RAM | > 85%, swap ativo |
| **I/O** | Disk read/write, network in/out | Latência de disco > 50ms |
| **GC** (Garbage Collection) | Pausas de memória | Pausas > 100ms, frequência alta |

### 5.3 Padrões de Performance

| Padrão | Problema | Solução |
|--------|----------|---------|
| **N+1 queries** | Query em loop | Eager loading, batching |
| **Unbounded data** | Carregar tudo | Paginação, streaming |
| **Synchronous wait** | Bloqueio desnecessário | Async/await, parallel processing |
| **Expensive computation** | Cálculo repetido | Memoization, cache |
| **Chatty I/O** | Muitas chamadas | Batch requests, pipeline |
| **Large payloads** | Muita transferência | Pagination, projection, compression |
| **Lock contention** | Concorrência | Lock-free structures, sharding |
| **Connection churn** | Criar/fechar conexões | Connection pooling |

### 5.4 Database Performance
- **Índices**: B-tree para range, Hash para equality, GiST para full-text
- **EXPLAIN ANALYZE**: sempre verifique planos de execução
- **Índices compostos**: coluna mais seletiva primeiro
- **Partial indexes**: `CREATE INDEX ... WHERE status = 'active'`
- **Covering indexes**: inclua colunas no `INCLUDE` para index-only scans
- **Sharding**: horizontal (por tenant, região) ou vertical (por funcionalidade)
- **Read replicas**: separe leituras de escritas

```
-- Antes (Table Scan ~ 500ms):
SELECT * FROM orders WHERE status = 'pending';

-- Depois (Index Scan ~ 5ms):
CREATE INDEX idx_orders_status ON orders(status);
```

### 5.5 Web Performance
| Técnica | Impacto | Esforço |
|---------|---------|---------|
| **Code splitting** | Reduz JS inicial | Médio |
| **Tree shaking** | Remove código morto | Baixo (automático) |
| **Lazy loading** | Carrega sob demanda | Baixo |
| **Image optimization** | Reduz peso de imagens | Baixo |
| **CDN** | Distribuição geográfica | Médio |
| **Prefetch/Prerender** | Antecipa navegação | Baixo |
| **HTTP/2 multiplex** | Paraleliza requests | Configuração |
| **Bundle compression** | gzip/brotli | Automático |
| **Critical CSS inline** | Renderiza acima da dobra | Médio |
| **Service Worker** | Offline, cache | Alto |

---

## 6. Segurança Técnica

### 6.1 OWASP Top 10 (para desenvolvedores)
| Risco | Prevenção |
|-------|-----------|
| **Injection (SQL, NoSQL, OS)** | Prepared statements, ORM, input validation |
| **Broken Authentication** | Hash + salt, MFA, session management, rate limiting |
| **Sensitive Data Exposure** | HTTPS (TLS 1.3), criptografia em repouso, não logar secrets |
| **XXE** | Desabilitar XML external entities |
| **Broken Access Control** | RBAC/ABAC, verificar em toda requisição |
| **Security Misconfiguration** | Hardening, minimal privileges, security headers |
| **XSS** | Output encoding, CSP, HttpOnly cookies |
| **Insecure Deserialization** | Type checking, integrity checks, não aceitar de cliente |
| **Using Components with Known Vulns** | Dependabot, renovate, SBOM |
| **Insufficient Logging & Monitoring** | Log estruturado, alertas, audit trail |

### 6.2 Security Headers
```
Content-Security-Policy: default-src 'self'; script-src 'self' cdn.example.com
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 0
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=()
```

---

## 7. Formatos Técnicos

### 7.1 Documentação Técnica
```
# Título (Nome do componente/sistema)

## Visão Geral
Propósito, contexto e arquitetura em 2-3 parágrafos.

## Quick Start
```bash
git clone ...
npm install
npm run dev
```

## API
### `POST /api/resource`
Descrição do endpoint.
**Request**: `{ "field": "type" }`
**Response**: `{ "id": "string" }`
**Errors**: 400 (validation), 404 (not found)

## Arquitetura
[diagrama ou descrição]

## Monitoramento
Métricas, alertas, dashboards.

## Runbook
Procedimentos operacionais comuns.
```

### 7.2 ADR (Architecture Decision Record)
```markdown
# ADR-001: Usar PostgreSQL como banco principal

Status: Aceito

Contexto: Precisamos de um banco relacional com suporte a JSON,
transações ACID e mature ecossistema.

Decisão: PostgreSQL 16 será o banco principal.
Motivos: (1) Maturidade, (2) JSONB para dados semi-estruturados,
(3) Replicação nativa, (4) TimescaleDB extension para séries temporais.

Consequências:
✅ Transações ACID fortes
✅ Ecossistema maduro (ORM, ferramentas, hosting)
⚠️ Custo operacional maior que SQLite
❌ Não escala horizontalmente como Cassandra

Alternativas:
- MySQL (rejeitado: JSON support inferior, replication lag)
- MongoDB (rejeitado: sem joins, transações limitadas)
```

### 7.3 Post-mortem Template
```markdown
# Post-mortem: Título do Incidente

Data: YYYY-MM-DD
Duração: 2h30
Impacto: 15% dos usuários afetados, 5k erros

## Timeline
- 14:00 - Alerta de latência alta
- 14:05 - Gargalo identificado no database CPU
- 14:10 - Query lenta identificada
- 14:20 - Kill query problemática
- 14:30 - Rollback do deploy
- 16:30 - Sistema normalizado

## Causa Raiz
Deploy com migration sem índice causou full table scan.

## Ações
- [x] Rollback do deploy (imediato)
- [ ] Adicionar índice antes da migration
- [ ] Criar runbook para query analysis
- [ ] Adicionar alerta de full table scan

## Lições
1. Migrations devem incluir índice sempre
2. Monitorar queries lentas em prod
3. Review de migrações deve ser obrigatório
```

---

## 8. Comandos e Ferramentas Técnicas

### 8.1 Debugging e Profiling
```bash
# CPU profiling
perf top
htop

# Memory
valgrind --leak-check=yes ./program
heaptrack ./program

# Disk I/O
iostat -x 1
iotop

# Network
tcpdump -i eth0 port 80
netstat -tulpn
ss -tulpn

# Database
EXPLAIN ANALYZE SELECT ...
pg_stat_activity  -- Postgres
SHOW PROCESSLIST; -- MySQL
```

### 8.2 Análise de Performance
```bash
# Web
curl -w "@curl-format.txt" -o /dev/null -s https://api.example.com
lighthouse https://example.com --output=json

# Node.js
node --prof app.js
node --inspect-brk app.js

# Python
python -m cProfile script.py
py-spy record -o profile.svg --pid 12345

# Linux
strace -p 1234
lsof -p 1234
```

---

## 9. Checklist de Qualidade Técnica

- [ ] Solução resolve a causa raiz, não o sintoma
- [ ] Complessidade justificada (não superengenharia)
- [ ] Tratamento de erros em todas as camadas
- [ ] Input validation em toda entrada externa
- [ ] Logs com níveis apropriados (info, warn, error)
- [ ] Métricas para monitorar em produção
- [ ] Testes para casos normais, borda e erro
- [ ] Documentação mínima para manutenção
- [ ] Performance avaliada com dados reais
- [ ] Segurança: injection, XSS, auth, rate limiting
- [ ] Rollback possível e testado
- [ ] Dependências atualizadas e auditadas

---

*Baseado em ciência da computação (Cormen, Tanenbaum), engenharia de software (Martin, Fowler, Hunt), design de sistemas (Kleppmann, Nygard), performance (Gregg, Souders) e segurança (OWASP, Stallings). Adaptado para pt-br em formato multi-ferramenta.*
