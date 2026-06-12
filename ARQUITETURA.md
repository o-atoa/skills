# Skill: Arquitetura de Software

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
│          Repositories / Gateway      │  ── Interfaces
├──────────────────────────────────────┤
│      Database / External Services    │  ── Infraestrutura
└──────────────────────────────────────┘
```

**Regra de Ouro**: Dependências apontam para dentro. O domínio nunca sabe sobre o mundo externo.

### Camadas e Responsabilidades

| Camada | Responsabilidade | Depende de |
|--------|-----------------|------------|
| **Controller/API** | Receber requisições, validar input, formatar resposta | Use Cases |
| **Use Case** | Orquestrar fluxos, coordenar entidades | Domain |
| **Domain** | Regras de negócio puras, entidades, value objects | Nada (puro) |
| **Repository** | Interface de persistência | Domain |
| **Infrastructure** | Implementar DB, APIs externas, filas | Repository (interface) |

### CQRS (Command Query Responsibility Segregation)

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

### Event-Driven Architecture

```
        ┌──────────┐
        │ Producer │─── Event ──→ Event Bus
        └──────────┘                │
                              ┌─────┴──────┐
                              ▼            ▼
                          Consumer 1   Consumer 2
```

## Princípios e Heurísticas

### SOLID

| Princípio | O que significa | Exemplo de violação |
|-----------|----------------|---------------------|
| **S** - Single Responsibility | Uma classe, um motivo para mudar | Classes Deus que fazem tudo |
| **O** - Open/Closed | Aberto para extensão, fechado para modificação | Adicionar if/else para cada novo tipo |
| **L** - Liskov Substitution | Subtipo deve substituir tipo base | Herança que quebra contrato |
| **I** - Interface Segregation | Muitas interfaces específicas > uma genérica | Interface gigante que força implementar métodos não usados |
| **D** - Dependency Inversion | Depender de abstrações, não implementações | new() concreto dentro da classe |

### Dicas Práticas

1. **Testabilidade** → Se é difícil testar, o design provavelmente está errado
2. **Acoplamento** → Prefira acoplamento fraco (eventos, interfaces, injeção)
3. **Coesão** → Coisas que mudam juntas ficam juntas
4. **YAGNI** → Não implemente o que não precisa hoje
5. **DRY** → Não repita lógica, mas repetição acidental é melhor que abstração prematura
6. **Lei de Conway** → Sistemas espelham a comunicação da organização

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
- [ ] As responsabilidades estão claramente separadas?
- [ ] As dependências apontam na direção correta?
- [ ] Novas funcionalidades podem ser adicionadas sem modificar código existente?
- [ ] O sistema é testável em cada camada?

### Performance
- [ ] Onde estão os gargalos potenciais?
- [ ] A estratégia de cache está definida?
- [ ] Consultas N+1 foram identificadas?
- [ ] O padrão de concorrência é adequado?

### Segurança
- [ ] Autenticação e autorização estão na camada correta?
- [ ] Dados sensíveis estão protegidos?
- [ ] Input validation está em todas as entradas?
- [ ] Rate limiting está implementado onde necessário?

### Manutenibilidade
- [ ] Um novo desenvolvedor entende o fluxo principal em 30 minutos?
- [ ] A documentação reflete o estado atual?
- [ ] Há dívida técnica documentada?
- [ ] ADRs existem para decisões importantes?

### Operação
- [ ] Monitoria e métricas estão implementadas?
- [ ] Logs são estruturados e pesquisáveis?
- [ ] O deployment é seguro e reversível?
- [ ] Há estratégia de disaster recovery?

## Checklist de Documentação

- [ ] Diagrama C4 (Contexto, Container, Component, Code) disponível
- [ ] ADRs criados para decisões significativas
- [ ] README.md atualizado com visão geral
- [ ] API documentada (Swagger/OpenAPI)
- [ ] Fluxos de dados documentados (entrada → processamento → saída)
- [ ] Contratos entre serviços definidos
- [ ] Estratégia de banco de dados documentada
- [ ] Plano de evolução/migration path descrito
