# ARQUITETURA — Arquitetura de Software

## Objetivo
Orientar agentes de IA no projeto, documentação, revisão e evolução da arquitetura de software, garantindo decisões conscientes e bem documentadas.

## Quando usar
- Ao projetar um novo sistema ou serviço
- Ao documentar arquitetura existente
- Ao revisar decisões arquiteturais
- Ao planejar refatorações significativas
- Ao avaliar trade-offs entre abordagens

## Documentação de Arquitetura

### Formato ADR (Architecture Decision Record)

ADRs documentam decisões arquiteturais importantes e seu contexto.

```markdown
# ADR-00XX: Título da Decisão

## Status
[ Proposto | Aceito | Depreciado | Substituído ]

## Contexto
Descreva o problema, o contexto técnico e de negócio,
as restrições e forças atuando nesta decisão.

## Decisão
Descreva a decisão tomada. Use linguagem clara e direta.
"Decidimos por [opção X] porque [motivo principal]."

## Consequências
- ✅ [Impacto positivo 1]
- ✅ [Impacto positivo 2]
- ❌ [Impacto negativo 1 / trade-off]
- ⚠️ [Risco ou consideração futura]

## Alternativas Consideradas
### Opção A: [nome]
Prós: ... | Contras: ...
### Opção B: [nome]
Prós: ... | Contras: ...

## Referências
- Links para docs, issues, PRs relevantes
```

### Gerenciamento de ADRs
- Diretório `docs/adr/` com numeração sequencial
- ADR aceito é imutável — correções viram novos ADRs
- Revise periodicamente para identificar decisões obsoletas

### Template para Revisão de Arquitetura

```markdown
## Visão Geral
[Diagrama ou descrição de alto nível]

## Pontos Fortes
- [O que está bem projetado]

## Pontos de Atenção
- [Riscos, dívida técnica, acoplamento]

## Sugestões
- [Melhorias propostas com justificativa]

## Perguntas em Aberto
- [Questões que precisam de esclarecimento]
```

## Documentação com C4 Model

O modelo C4 organiza a arquitetura em 4 níveis:

| Nível | Público | Conteúdo |
|-------|---------|----------|
| **Contexto** (N1) | Todos | Sistema, usuários, sistemas externos |
| **Container** (N2) | Técnico | Aplicações, bancos, filas, serviços |
| **Component** (N3) | Devs | Módulos internos de cada container |
| **Code** (N4) | Devs | Diagramas de classe específicos |

Use notação C4-PlantUML ou Structurizr.

### arc42 Template
Template de 12 seções: Introdução, Restrições, Escopo/Contexto, Estratégia, Blocos, Runtime, Deployment, Cross-cutting, ADRs, Qualidade, Riscos, Glossário.

### 4+1 Views (Kruchten)
- **Visão Lógica**: Estrutura OO, classes, pacotes
- **Visão de Processo**: Concorrência, threads, sincronização
- **Visão de Desenvolvimento**: Módulos, camadas, organização do código
- **Visão Física**: Deployment, nós, rede
- **Visão de Cenários**: Casos de uso que unificam as visões

## Padrões Arquiteturais

### Clean Architecture / Arquitetura Hexagonal

```
┌──────────────────────────────────────┐
│          Controllers / API           │  ── Camada de entrada
├──────────────────────────────────────┤
│           Use Cases / Services       │  ── Casos de uso
├──────────────────────────────────────┤
│            Domain / Entities         │  ── Núcleo do negócio
├──────────────────────────────────────┤
│          Repositories / Gateway      │  ── Interfaces (Ports)
├──────────────────────────────────────┤
│      Database / External Services    │  ── Infraestrutura (Adapters)
└──────────────────────────────────────┘
```

**Regra de Ouro**: Dependências apontam para dentro. O domínio nunca sabe sobre o mundo externo.

### Onion vs Clean Architecture
Ambas priorizam o domínio no centro. Onion usa anéis concêntricos (Domain → Application Services → Infrastructure). Clean formaliza camadas com boundaries explícitos. Onion é conceitual; Clean é mais prescritiva. Escolha Clean para múltiplos adapters; Onion para projetos menores.

### Camadas e Responsabilidades

| Camada | Responsabilidade | Depende de |
|--------|-----------------|------------|
| **Controller/API** | Receber requisições, validar input, formatar resposta | Use Cases |
| **Use Case** | Orquestrar fluxos, coordenar entidades | Domain |
| **Domain** | Regras de negócio puras, entidades, value objects | Nada (puro) |
| **Repository** | Interface de persistência | Domain |
| **Infrastructure** | Implementar DB, APIs externas, filas | Repository (interface) |

### CQRS

```
Comando (escrita)         Query (leitura)
      │                         │
      ▼                         ▼
   CommandBus               QueryBus
      │                         │
      ▼                         ▼
   CommandHandler          QueryHandler
      │                         │
      ▼                         ▼
   Write DB ◄──────────► Read DB (eventual consistency)
```

Separa modelos de leitura e escrita. Use quando leitura e escrita têm cargas e padrões muito diferentes. Evite se o CRUD simples atende.

### CQRS + Event Sourcing
Commands geram eventos no Event Store; queries leem projeções materializadas. Event Store é a fonte da verdade; Read Models são descartáveis. Ideal para auditoria e rastreabilidade.

### Event-Driven Architecture

```
        ┌──────────┐
        │ Producer │─── Event ──→ Event Bus
        └──────────┘                │
                              ┌─────┴──────┐
                              ▼            ▼
                          Consumer 1   Consumer 2
```

### Event Sourcing
Estado como sequência imutável de eventos, não snapshot atual.

**Benefícios**: Auditoria completa, reconstrução histórica, rastreabilidade, desacoplamento temporal.

**Desafios**: Versionamento de eventos, consistência eventual, queries complexas (exigem projeções).

**Quando usar**: Sistemas financeiros, auditoria regulatória. Evitar em CRUDs simples ou domínios com consistência forte.

### Saga Pattern

| Aspecto | Coreografia | Orquestração |
|---------|-------------|--------------|
| Coordenação | Eventos entre serviços | Coordenador central |
| Acoplamento | Baixo | Médio |
| Visibilidade | Difícil | Clara |
| Ideal para | Fluxos simples, times autônomos | Fluxos complexos, controle exigido |

### Saga com Compensação
Cada passo tem ação compensatória (`ReservarEstoque` → `EstornarReserva`). É rollforward semântico, não rollback técnico. Projete compensações como idempotentes.

### Strangler Fig Pattern
Migração gradual de um sistema legado: intercepta chamadas, roteia novas funcionalidades para o novo sistema, mantém funcionalidades estáveis no legado. Remove funcionalidades do legado conforme são migradas. Eventualmente todo o tráfego vai para o novo sistema.

**Quando usar**: Migrações de monolitos para microsserviços, substituição de sistemas legados, mudanças incrementais com baixo risco.

### Backends for Frontends (BFF)
Um backend específico por frontend (web, mobile, IoT). Cada BFF conhece formato, payload e padrão de uso do cliente.

**Benefícios**: Otimização por cliente, isolamento de mudanças, times frontend autônomos.

**Desafios**: Duplicação de lógica, mais serviços.

### Sidecar / Ambassador

**Sidecar**: Container auxiliar junto ao principal (ex: proxy, coletor de logs). Compartilha ciclo de vida e rede.

**Ambassador**: Proxy que media comunicação externa com retry, circuit breaker e autenticação sem modificar o serviço.

### Anti-Corruption Layer
Tradução entre sistema novo e legado. Impede contaminação de modelos com isolamento semântico. Use em integrações com legados, aquisições, migrações.

### Publisher-Subscriber (Avançado)

| Garantia | Descrição |
|----------|-----------|
| **At-least-once** | Mensagem entregue 1+ vezes (requer idempotência) |
| **Exactly-once** | Entregue exatamente 1 vez (mais caro) |
| **Ordered delivery** | Na ordem publicada |
| **Dead letter queue** | Mensagens com falha para análise |

Use filtros de tópicos, fanout e partições para paralelismo.

### Circuit Breaker
Protege o sistema contra falhas em cascata. Três estados: **Fechado** (requisições passam), **Aberto** (requisições falham rápido), **Meio-aberto** (testa recuperação). Configure thresholds de falha e timeouts de recuperação.

### Bulkhead
Isola recursos em pools separados (conexões por cliente, threads por serviço). Falha em um componente não consome recursos dos outros. Particição por criticidade e perfil de uso.

## Arquitetura de Microsserviços

### Microservices vs Modular Monolith

| Aspecto | Microsserviços | Modular Monolith |
|---------|---------------|------------------|
| Deployment | Independente | Único |
| Escalabilidade | Granular | Vertical |
| Complexidade | Alta | Baixa |
| Latência | Rede | Memória |
| Isolamento | Alto | Baixo |
| Consistência | Eventual | Imediata |

Comece com Modular Monolith; migre quando a modularização da equipe exigir.

### Event-Driven, Service Mesh, Serverless
Microsserviços event-driven usam Kafka/RabbitMQ para desacoplamento temporal. Service Mesh (Istio, Linkerd) adiciona descoberta, mTLS e tracing sem modificar código. Serverless (AWS Lambda) é ideal para workloads event-driven e batch; evite para stateful ou baixa latência (cold start).

## Domain-Driven Design (DDD)

### Ubiquitous Language
Linguagem compartilhada entre devs, experts e stakeholders. Termos do domínio usados consistentemente em código, docs e conversas. Base de todo DDD.

### Bounded Contexts
Delimitação explícita de onde um modelo de domínio se aplica. Padrões de comunicação:
- **Shared Kernel**: Subconjunto compartilhado
- **Customer/Supplier**: Um fornece, outro consome
- **Conformist**: Adapta-se ao modelo alheio
- **Separate Ways**: Sem integração
- **Open/Host Service**: API pública

### Aggregates e Aggregate Roots
Cluster de objetos tratados como unidade. O Aggregate Root é a única entrada. Regras: referencie só o root, consistência imediata interna (eventual entre aggregates), agregados pequenos (2-5 entidades).

### Domain Events
Fatos relevantes no domínio (`PedidoConfirmado`, `PagamentoRecebido`). Use para comunicação entre aggregates ou bounded contexts. Nomeie no passado, inclua timestamp e ID único.

### Value Objects vs Entities

| Característica | Value Object | Entity |
|---------------|-------------|--------|
| Identidade | Pelo valor | Pelo ID |
| Mutabilidade | Imutável | Mutável |
| Igualdade | Todos atributos | ID |
| Exemplo | `Money(100, "BRL")` | `Pedido(id=42)` |

### Repositories e Factories
- **Repository**: Abstrai persistência, devolve aggregates
- **Factory**: Encapsula construção complexa de objetos
- Repositories operam sobre aggregates; Factories criam objetos

### Strategic vs Tactical DDD
- **Strategic**: Bounded Contexts, Context Maps, Ubiquitous Language — fronteiras e relacionamentos
- **Tactical**: Entities, Value Objects, Aggregates, Domain Events, Repositories — implementação do modelo

## Avaliação de Arquitetura

### ATAM (Architecture Trade-off Analysis Method)
1. Apresentação da arquitetura
2. Identificação de drivers arquiteturais
3. Brainstorming de cenários de qualidade
4. Mapeamento cenários → decisões
5. Análise de trade-offs
6. Documentação

Produto: utility tree com cenários priorizados e riscos.

### SAAM (Software Architecture Analysis Method)
Avalia a adequação da arquitetura para cenários de modificação. Mais simples que ATAM. Útil para comparação entre arquiteturas candidatas.

### Quality Attribute Scenarios
Formato: **Fonte** → **Estímulo** → **Ambiente** → **Artefato** → **Resposta** → **Medida**.

Exemplo: "Quando 1000 usuários simultâneos acessam em horário de pico, a API de pedidos deve responder em <200ms para 99% das requisições."

### Utility Tree
Atributos de qualidade organizados em cenários priorizados:
```
Performance
├── Latência <200ms p/ 95% req (Alta)
├── Throughput 1000 req/s (Média)
Modificabilidade
├── Nova fonte de pagamento em 2 semanas (Alta)
Segurança
└── Dados criptografados em repouso (Alta)
```

### Architecture Fitness Functions
Testes automatizados que validam características arquiteturais em CI:
- **Acoplamento**: Imports entre módulos ≤ limite
- **Dependência**: Sem ciclos entre pacotes
- **Tamanho**: Módulos ≤ X linhas
- **Layers**: Camada X não importa da Z
- **Resiliência**: Timeout em todo cliente HTTP

## Anti-Patterns Arquiteturais

| Anti-Pattern | Descrição | Solução |
|-------------|-----------|---------|
| **Big Ball of Mud** | Sistema sem estrutura, código spaghetti | Refatorar com boundaries claros; extrair módulos |
| **God Class / God Service** | Classe ou serviço central que concentra excesso de responsabilidade | Extrair responsabilidades; aplicar SRP |
| **Distributed Monolith** | Microsserviços que dependem fortemente uns dos outros; deploy acoplado | Revisar boundaries; consolidar módulos coesos |
| **Golden Hammer** | Usar a mesma tecnologia para todo problema | Avaliar cada problema no contexto; diversificar ferramentas |
| **Analysis Paralysis** | Analisar infinitamente sem implementar | Definir timebox para análise; ciclos curtos de implementação |
| **Premature Optimization** | Otimizar antes de medir gargalos reais | Medir primeiro; otimizar baseado em evidências |
| **Vendor Lock-in** | Dependência excessiva de um fornecedor específico | Abstrair com interfaces; preferir padrões abertos |

## Non-Functional Requirements (NFRs)

### Como Documentar NFRs
Use Quality Attribute Scenarios com contexto, estímulo, métrica e critério de aceite. Diferencie obrigatórios de desejáveis.

### Performance e Escalabilidade
- Latência (p95, p99), throughput, capacidade de pico
- Estratégias: cache, leitura assíncrona, escalabilidade horizontal
- Defina SLIs e SLOs

### Segurança
- Autenticação (OAuth2, SAML, JWT)
- Autorização (RBAC, ABAC)
- Criptografia em trânsito (TLS) e repouso (AES-256)
- Rate limiting, input validation

### Disponibilidade e Confiabilidade
- SLAs/SLOs de uptime (99.9%, 99.99%)
- Disaster recovery: RPO e RTO
- Redundância, failover automático, health checks

### Compliance
- LGPD, GDPR, HIPAA, PCI-DSS
- Data residency, right to delete, audit logging
- Documentar controles e evidenciar conformidade

### Medindo Qualidades Arquiteturais
- **Manutenibilidade**: Tempo médio para mudança padrão
- **Testabilidade**: Cobertura, tempo de execução
- **Deployabilidade**: Tempo de deploy, taxa de sucesso
- **Escalabilidade**: Curva de latência vs carga
- **Resiliência**: MTBF, MTTR, taxa de erro

## Architecture Decision Framework

Passo a passo com análise de trade-offs:

1. **Definir problema**: Qual decisão? Contexto e restrições?
2. **Levantar requisitos**: Funcionais + NFRs relevantes. Priorize (MoSCoW).
3. **Gerar alternativas**: 2-5 opções viáveis.
4. **Definir critérios**: Atributos de qualidade (performance, segurança, custo, complexidade).
5. **Avaliar trade-offs**: Matriz peso × nota por alternativa.
6. **Escolher e documentar**: ADR com justificativa.
7. **Implementar e validar**: Meça resultados. Crie fitness functions.
8. **Revisar**: Decisões envelhecem; reavalie quando o contexto mudar.

## Princípios e Heurísticas

### SOLID

| Princípio | O que significa | Exemplo de violação |
|-----------|----------------|---------------------|
| **S** - Single Responsibility | Uma classe, um motivo para mudar | Classes Deus que fazem tudo |
| **O** - Open/Closed | Aberto para extensão, fechado para modificação | Adicionar if/else para cada novo tipo |
| **L** - Liskov Substitution | Subtipo deve substituir tipo base | Herança que quebra contrato |
| **I** - Interface Segregation | Muitas interfaces específicas > uma genérica | Interface gigante que força implementar métodos não usados |
| **D** - Dependency Inversion | Depender de abstrações, não implementações | `new()` concreto dentro da classe |

### Dicas Práticas
1. **Testabilidade** → Difícil de testar = design errado
2. **Acoplamento** → Prefira fraco (eventos, interfaces, injeção)
3. **Coesão** → Coisas que mudam juntas ficam juntas
4. **YAGNI** → Não implemente o que não precisa hoje
5. **DRY** → Não repita lógica; repetição acidental < abstração prematura
6. **Lei de Conway** → Sistemas espelham a comunicação da organização
7. **Postel** → Conservador no envio, liberal no recebimento

### Acoplamento vs Coesão

```
           Baixa Coesão ←────────→ Alta Coesão
               │                        │
 Baixo         │                        │
 Acoplamento   │    ⚠️ Médio            │ ✅ Ideal
     ↑         │                        │
     │         │                        │
     │         │                        │
 Alto          │                        │
 Acoplamento   │    ❌ Ruim             │    ⚠️ Médio
               │                        │
```

## Perguntas para Revisão Arquitetural

### Estrutura
- [ ] Responsabilidades separadas? Dependências na direção correta?
- [ ] OCP: novas funcionalidades sem modificar existentes?
- [ ] Sistema testável em cada camada? Bounded contexts definidos?

### Performance
- [ ] Gargalos potenciais identificados? Estratégia de cache definida?
- [ ] N+1 queries? Padrão de concorrência adequado?

### Segurança
- [ ] AuthN/AuthZ na camada correta? Dados sensíveis protegidos?
- [ ] Input validation em todas as entradas? Rate limiting aplicado?

### Manutenibilidade
- [ ] Novo dev entende fluxo principal em 30 min?
- [ ] Documentação reflete estado atual? Dívida técnica documentada?
- [ ] ADRs existem para decisões importantes?

### Operação
- [ ] Monitoria e métricas implementadas? Logs estruturados?
- [ ] Deployment seguro e reversível? Disaster recovery planejado?
- [ ] Circuit breakers e bulkheads configurados?

## Checklist de Documentação

- [ ] Diagrama C4 (Contexto, Container, Component, Code) disponível
- [ ] ADRs criados para decisões significativas
- [ ] README.md atualizado com visão geral
- [ ] API documentada (Swagger/OpenAPI)
- [ ] Fluxos de dados documentados (entrada → processamento → saída)
- [ ] Contratos entre serviços definidos
- [ ] Estratégia de banco de dados documentada
- [ ] Plano de evolução/migration path descrito
- [ ] NFRs documentados com cenários de qualidade
- [ ] Diagrama de contexto (C4 N1) disponível para stakeholders
- [ ] Fitness functions configuradas em CI
