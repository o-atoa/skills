# Skills para Agentes de IA

> Repositório com 31 skills em Português (pt-br) para orientar agentes de IA na execução de tarefas de engenharia de software. Conteúdo condensado de 30+ sistemas de prompting de OpenAI, Anthropic, Google, xAI, Devin, Manus, Cursor, Windsurf, Cline, Replit e mais.

---

## Meta-skill (Instruções Gerais)

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [AGENTS.md](AGENTS.md) | Instruções mestre — filosofia, ciclo do agente, código, git, ferramentas, testes, segurança. Hierarquia de instruções e lembretes rápidos. | 214 |

---

## Workflow Skills (GitHub + Ciclo de Desenvolvimento)

| Skill | Descrição | Quando usar | Linhas |
|-------|-----------|-------------|-------:|
| [ISSUES.md](ISSUES.md) | Templates de bug/feature/task, labels, prioridades (P0-P3), CLI do GitHub | Criar, triar ou resolver issues | 148 |
| [PR.md](PR.md) | Template de PR, convenção de commits, revisão (blocker vs nit), merge estratégias, resolução de conflitos, CI/CD | Criar, revisar ou fazer merge de PRs | 166 |
| [TESTS.md](TESTS.md) | Pirâmide de testes, Triple A, mocking, TDD, property-based, mutation, contrato, testes visuais, BDD, design de casos, CI/CD | Escrever, executar ou manter testes | 442 |
| [ARQUITETURA.md](ARQUITETURA.md) | ADRs, Clean Arch, Hexagonal, CQRS, Event Sourcing, Saga, DDD, C4 Model, ATAM, anti-patterns, NFRs | Projetar, documentar ou revisar arquitetura | 445 |
| [ENGENHARIA.md](ENGENHARIA.md) | Ciclo SDLC completo, análise/impacto, planejamento, implementação, deploy (blue-green, canary), monitoria, segurança (SAST/DAST) | Workflow geral de engenharia | 282 |

---

## Skills por Tema (Genéricas para qualquer Agente/IA)

### Comportamento e Comunicação

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [COMPORTAMENTO.md](COMPORTAMENTO.md) | Postura, comunicação, modos de operação, ética, gestão de erros, personalização, adaptação cultural | 246 |
| [CONVERSA.md](CONVERSA.md) | Personalidade, tom, valores (verdade, beleza, respeito, diversão), tópicos sensíveis, estilo de escrita | 115 |
| [VOZ.md](VOZ.md) | Design para fala, granularidade emocional, gerenciamento de turno, reparo, prosódia, acessibilidade, cultura | 211 |
| [DIRETO.md](DIRETO.md) | Concisão máxima, templates de resposta, eliminação de redundância, tabelas de decisão, exemplos antes/depois | 203 |
| [TEXTO.md](TEXTO.md) | Escrita colaborativa, revisão, estilo, citações, classificação de dados, assistência de escrita | 144 |

### Raciocínio e Técnica

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [RAZÃO.md](RAZAO.md) | Lógica formal (dedução, indução, abdução), MECE, Primeiros Princípios, Cynefin, tomada de decisão, vieses, RCA, 5 Whys, TRIZ, pensamento sistêmico, método científico | 465 |
| [TÉCNICO.md](TECNICO.md) | Algoritmos e complexidade, design de sistemas, capacity planning, cache, resiliência, REST/GraphQL/gRPC, performance, segurança (OWASP), debugging | 539 |
| [CODIGO.md](CODIGO.md) | Nomenclatura por linguagem, estrutura de projetos, imports, funções, erros, TypeScript strict, segurança em código, performance | 258 |

### Dados e Backend

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [DADOS.md](DADOS.md) | Modelagem (normalização, ER, índices, constraints), SQL avançado (CTEs, window functions), escolha de banco, migrações, performance, segurança, testing | 295 |
| [FONTES.md](FONTES.md) | Busca web, avaliação de fontes, colaboração em equipe, vieses de mídia, ética e segurança | 90 |

### Web, UI e Aplicações

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [WEB.md](WEB.md) | HTML/CSS, Tailwind, React, componentes shadcn/ui, jogos HTML, Three.js, boas práticas de UI | 136 |
| [APPS.md](APPS.md) | Editor de apps web, fluxo de código, integrações (banco, busca, imagem), notas técnicas JSX | 74 |
| [INTERFACE.md](INTERFACE.md) | Componentes MDX, Projetos de Código, acessibilidade, diagramas, executável Node.js, ações sugeridas | 146 |

### Navegação, Automação e Ferramentas

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [NAVEGAR.md](NAVEGAR.md) | Navegação web, multimodal (imagens), interpretador Python, canvas, citações | 76 |
| [AUTOMAÇÃO.md](AUTOMACAO.md) | Automação de navegador: GOTO, CLICK, TYPE, SCROLL, memorização, contagem | 82 |
| [FERRAMENTAS.md](FERRAMENTAS.md) | Busca semântica, leitura de arquivos, terminal, regex, edição, APIs externas, gerenciamento de tarefas | 150 |
| [EDIÇÃO.md](EDICAO.md) | Edição de arquivos, SEARCH/REPLACE, ferramentas, workflow passo a passo, navegador, MCP | 211 |

### Pesquisa e Documentação

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [PESQUISA.md](PESQUISA.md) | Metodologia científica, PICO/FINER, revisão sistemática (PRISMA), avaliação CRAAP, vieses, síntese GRADE, reprodutibilidade FAIR, IMRaD | 251 |
| [MARCAÇÃO.md](MARCACAO.md) | Markdown avançado (tabelas, footnotes, Mermaid), diagramas, documentação (README, ADR, runbook), acessibilidade, segurança XSS | 553 |

### Workflows e Projetos

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [WORKFLOW.md](WORKFLOW.md) | Git workflow rigoroso: sincronização, instalação travada, portão de qualidade, PR obrigatório, verificação de segurança | 128 |
| [ITERAR.md](ITERAR.md) | Desenvolvimento iterativo, edição de arquivos, geração de código, debugging, políticas de dados e proatividade | 132 |
| [PROJETO.md](PROJETO.md) | Inicialização de projetos (Vite, Next, shadcn), clonagem de sites, deploy, Neon PostgreSQL, scraping | 166 |

### Agentes Especializados

| Skill | Descrição | Linhas |
|-------|-----------|-------:|
| [AGENTE.md](AGENTE.md) | Agente autônomo completo: planejamento, conhecimento, dados, shell, deploy, navegador, tarefas longas | 188 |
| [CONTEXTO.md](CONTEXTO.md) | Memória persistente, busca semântica, terminal, regex, edição, web, deploy, API externa | 172 |

---

## Como usar

Cada arquivo é autocontido e escrito para ser interpretado por qualquer agente ou LLM. Basta referenciar o caminho no prompt:

```
Siga as instruções em skills/PR.md para criar um pull request.
Use skills/RAZAO.md para estruturar seu raciocínio antes de decidir.
Use skills/TECNICO.md como referência de design de sistemas.
Use skills/COMPORTAMENTO.md como guia de comunicação.
```

Skills mais densas podem ser referenciadas por seção específica:
```
Use skills/DADOS.md#sql-avançado para otimizar queries complexas.
Use skills/ARQUITETURA.md#ddd-domain-driven-design para modelagem tática.
Use skills/TESTS.md#test-design-techniques para cobertura de casos de borda.
```

---

## Origem

Conteúdo adaptado e condensado de prompts de sistema de: OpenAI (o1, GPT-4, GPT-4o), Anthropic (Claude), Google (Gemini), xAI (Grok), DeepSeek, Perplexity, Cursor, Windsurf, Devin, Manus, Replit, Cline, Codex, e mais. Consolidado em formato multi-ferramenta pt-br.

---

## Licença

AGPL v3

## Como usar

Cada arquivo é autocontido e escrito para ser interpretado por qualquer agente ou LLM. Basta referenciar o caminho no prompt:

```
Siga as instruções em skills/PR.md para criar um pull request.
Use skills/AGENTS.md como guia geral de comportamento.
Use skills/COMPORTAMENTO.md como referência de comportamento do agente.
```

## Origem

Conteúdo adaptado do projeto CL4R1T4S (sistem prompts extraídos de IAs como OpenAI, Google, Anthropic, xAI, Perplexity, Cursor, Windsurf, Devin, Manus, Replit e mais).

## Licença

AGPL v3
