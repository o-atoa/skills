# DADOS — Banco de Dados, Modelagem e Backend

> Guia de banco de dados, SQL, performance e backend. Abrange modelagem até produção.

---

## 1. Modelagem de Dados

### Normalização

| Forma | Regra | Violação |
|-------|-------|----------|
| **1NF** | Colunas atômicas, linhas únicas | `telefones: "11-9999,21-8888"` |
| **2NF** | Não-chaves dependem da chave **inteira** | `pedidos(produto_id, fornecedor)` |
| **3NF** | Não-chaves dependem **só** da chave | `funcionarios(dept_id, dept_nome)` |

Exemplo: `pedidos(id, cliente, cliente_email, produto, categoria, data)` → `clientes(id, nome, email)`, `produtos(id, nome, categoria_id)`, `categorias(id, nome)`, `pedidos(id, cliente_id, produto_id, data)`.

### Desnormalização

Use em read-heavy, data warehouse, dashboards, cache. Riscos: anomalias de escrita.

### Chaves

Surrogate (`id UUID DEFAULT gen_random_uuid()`) como padrão. Natural (CPF, email) como `UNIQUE` complementar. Composta: `PRIMARY KEY (pedido_id, produto_id)`. FK: `REFERENCES clientes(id)`.

### Índices

B-tree (range, ORDER BY), Hash (igualdade exata), GiST (geo, intervalos), GIN (JSONB, arrays), BRIN (logs), Covering (`INCLUDE`), Partial (`WHERE status = 'pendente'`), Composite (`col1, col2` — igualdade 1º).

### Constraints

```sql
CHECK (idade >= 0), UNIQUE (email), NOT NULL
EXCLUDE USING gist (periodo WITH &&)
```

### Sequences

```sql
CREATE TABLE pedidos (id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY);
```

### EAV — Anti-pattern

`atributos(entidade_id, nome, valor)` sem tipo nem integridade. Use JSONB + GIN.

---

## 2. SQL Avançado

### CTEs

```sql
WITH vendas AS (
    SELECT date_trunc('month', data) mes, SUM(total) total FROM pedidos GROUP BY mes
) SELECT * FROM vendas WHERE total > 1000;

WITH RECURSIVE tree AS (
    SELECT id, nome, parent_id FROM categorias WHERE id = 1
    UNION ALL
    SELECT c.id, c.nome, c.parent_id
    FROM categorias c JOIN tree t ON c.parent_id = t.id
) SELECT * FROM tree;
```

No PG, CTEs materializam. Use `NOT MATERIALIZED` se leve.

### Window Functions

```sql
SELECT nome, dept, salario,
    ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salario DESC) AS rank,
    LAG(salario) OVER (PARTITION BY dept ORDER BY data) AS anterior,
    SUM(salario) OVER (PARTITION BY dept) AS total_dept,
    AVG(salario) OVER (ORDER BY data ROWS BETWEEN 3 PRECEDING AND CURRENT ROW) AS media_movel
FROM funcionarios;
```

### JOINS vs LATERAL

JOIN = melhor performance. LATERAL = top-N por grupo. Evite subquery correlacionada.

```sql
SELECT c.nome, p.data, p.total
FROM clientes c
LEFT JOIN LATERAL (SELECT data, total FROM pedidos WHERE cliente_id = c.id ORDER BY data DESC LIMIT 3) p ON true;
```

### Full-Text Search

```sql
ALTER TABLE artigos ADD COLUMN busca TSVECTOR GENERATED ALWAYS AS
    (to_tsvector('portuguese', titulo || ' ' || corpo)) STORED;
CREATE INDEX ON artigos USING GIN (busca);
SELECT titulo FROM artigos WHERE busca @@ plainto_tsquery('portuguese', 'banco de dados');
```

### JSONB & Arrays

```sql
SELECT dados->>'nome' FROM usuarios WHERE dados @> '{"plano":"premium"}'::jsonb;
SELECT * FROM produtos WHERE tags @> ARRAY['urgente'];
```

### EXPLAIN ANALYZE

Seq Scan = ruim (falta índice). Index Only Scan = ótimo. Hash Join = sem índice. Alerta: `rows=X loops=Y` (subquery), Sort sem índice.

### Otimização

`EXISTS` > `IN`, `FILTER (WHERE)` > `CASE`, `UNION ALL` > `UNION`, evite `SELECT *` e `func(col)` em WHERE.

---

## 3. Bancos de Dados — Escolha

| Tipo | Banco | Força | Fraqueza |
|------|-------|-------|----------|
| Relacional | **PostgreSQL** | Extensível, JSONB, ACID | WAL serial |
| Relacional | **SQLite** | Zero-config, embedded | Sem concorrência |
| Document | **MongoDB** | Schema flexível | Sem joins |
| KV | **Redis** | Sub-ms, streams | Volátil |
| Graph | **Neo4j** | Relacionamentos complexos | Curva de aprendizado |
| TS | **TimescaleDB** | Métricas (extensão PG) | Nicho |
| Search | **Elasticsearch** | Full-text robusto | Operação complexa |

PG resolve 90%. Redis para cache. ES para search. TimescaleDB para time-series.

---

## 4. Migrações

### Estratégias

Incremental (dia a dia), squash (pós-estabilização), versionada (`up` + `down`). Ferramentas: `golang-migrate`, `flyway`, `alembic`, `prisma migrate`.

### Zero-Downtime

1. Add column: segura com `DEFAULT NULL`
2. Rename: crie nova, escreva em ambas, migre, remova antiga
3. Add index: `CREATE INDEX CONCURRENTLY`
4. Drop: marque deprecated, remova em janela
5. Change type: nova coluna, migre batch, troque

```sql
ALTER TABLE usuarios ADD COLUMN email_novo VARCHAR(255);
-- UPDATE batch
-- deploy que lê de email_novo
ALTER TABLE DROP COLUMN email;
ALTER TABLE RENAME COLUMN email_novo TO email;
```

Nunca edite migrações já aplicadas. Idempotência: `IF NOT EXISTS`.

---

## 5. Performance

### Processo

1. Identificar (`pg_stat_statements`, slow log)
2. Analisar (`EXPLAIN ANALYZE`)
3. Testar e medir

### Pooling

PgBouncer. Pool: `(cores * 2) + 1`. Leitura em réplicas, escrita no primário.

### Sharding & Caching

Horizontal (Citus, tenant_id) ou vertical. Cache-aside (padrão) ou write-through. Invalidação via TTL/evento.

### Materialized Views

```sql
CREATE MATERIALIZED VIEW vendas_diarias AS
SELECT data, SUM(total) total FROM pedidos GROUP BY data;
REFRESH MATERIALIZED VIEW CONCURRENTLY vendas_diarias;
```

### Partitioning

```sql
CREATE TABLE logs (id INT, data DATE) PARTITION BY RANGE (data);
CREATE TABLE logs_202401 PARTITION OF logs FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

Range (datas), List (regiões), Hash (carga).

---

## 6. Backend Architecture

### API & Layers

REST (CRUD), GraphQL (múltiplos consumers), gRPC (interna).  
`Controller → Service (regras, tx) → Repository (dados)`.

```typescript
class PedidoService {
    constructor(private repo: PedidoRepository) {}
    async criar(dto: CriarPedidoDTO): Promise<Pedido> {
        const pedido = Pedido.criar(dto);
        await this.repo.salvar(pedido);
        return pedido;
    }
}
```

### Repository

```typescript
interface PedidoRepository {
    buscarPorId(id: string): Promise<Pedido | null>;
    salvar(pedido: Pedido): Promise<void>;
}
```

### Transactions

Optimistic (versão), pessimistic (`FOR UPDATE`). Isolation: `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`.

### Jobs & Webhooks

BullMQ (Node), Sidekiq (Ruby), Celery (Python), ou `SKIP LOCKED` no PG.  
Webhooks: HMAC + idempotency key + retry exp backoff + dead letter.

### Rate Limiting

Fixed window, sliding window, token bucket, leaky bucket. Redis: `INCR + EXPIRE`.

---

## 7. Segurança

### SQL Injection

**Sempre parametrizado**: `db.query("SELECT ... WHERE email = $1", [email])`. Nunca concatenar.

### RLS

```sql
ALTER TABLE pedidos ENABLE ROW LEVEL SECURITY;
CREATE POLICY pedidos_tenant ON pedidos USING (tenant_id = current_setting('app.tenant_id')::INT);
```

### Encryption & Masking

TLS 1.3 in transit. `pgcrypto` at rest. Mascaramento via `regexp_replace` em views.

### Audit & Backup

Tabela `audit_log(tabela, operacao, dados_antigos JSONB, dados_novos JSONB)` populada por trigger.  
Backup: full (`pg_dump`), WAL (PITR, RPO segundos), snapshot. Teste restore periodicamente.

---

## 8. Testing

### Migrations & Queries

Teste `up + down + up`. Use tabelas temporárias:

```typescript
await db.query(`CREATE TEMPORARY TABLE test (id INT, total NUMERIC)`);
await db.query(`INSERT INTO test VALUES (1, 100)`);
const r = await db.query(`SELECT SUM(total) FROM test`);
assert.strictEqual(r.rows[0].sum, 100);
```

### Testcontainers

```typescript
const pg = await new PostgreSqlContainer("postgres:16").start();
const db = new Pool(pg.getConnectionUri());
```

### Seeds & Property-Based

Factories: `buildPedido({ total: 100 })`. Property-based para invariantes (ex: saldo nunca negativo).

---

## Checklist

- [ ] Schema 3NF (salvo desnormalização intencional)
- [ ] FKs com índices
- [ ] Migrações testadas (`up` + `down`)
- [ ] `EXPLAIN ANALYZE` revisado
- [ ] RLS habilitado e testado
- [ ] Connection pooling configurado
- [ ] Backup/PITR funcionais
- [ ] SQL injection prevenido
- [ ] Testes com banco real
