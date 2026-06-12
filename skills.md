# Skills para Agentes de IA

Este diretório contém skills em Português (pt-br) para orientar agentes de IA na execução de tarefas de engenharia de software.

## Meta

| Arquivo | Descrição |
|---------|-----------|
| [AGENTS.md](AGENTS.md) | Instruções gerais para agentes — melhor de 30+ prompts de sistema |

## Workflow Skills

| Skill | Descrição | Quando usar |
|-------|-----------|-------------|
| [ISSUES.md](ISSUES.md) | Gerenciamento de Issues no GitHub | Criar, triar ou resolver issues |
| [PR.md](PR.md) | Pull Requests no GitHub | Criar, revisar ou fazer merge de PRs |
| [TESTS.md](TESTS.md) | Escrita e execução de testes | Escrever, executar ou manter testes |
| [ARQUITETURA.md](ARQUITETURA.md) | Arquitetura de software | Projetar, documentar ou revisar arquitetura |

## Vendor Prompts (Extraídos e Adaptados)

Prompts de sistema extraídos e adaptados para pt-br em formato multi-ferramenta.

| Vendor | Arquivo | Foco |
|--------|---------|------|
| Anthropic | [ANTHROPIC.md](ANTHROPIC.md) | Claude (Sonnet, Opus) |
| Bolt | [BOLT.md](BOLT.md) | Bolt.new |
| Brave | [BRAVE.md](BRAVE.md) | Brave Leo |
| Cline | [CLINE.md](CLINE.md) | Cline (VS Code AI) |
| Cluely | [CLUELY.md](CLUELY.md) | Cluely AI |
| Cursor | [CURSOR.md](CURSOR.md) | Cursor IDE |
| Devin | [DEVIN.md](DEVIN.md) | Devin ( Cognition AI) |
| DIA | [DIA.md](DIA.md) | DIA AI |
| Factory | [FACTORY.md](FACTORY.md) | Factory / Droid |
| Google | [GOOGLE.md](GOOGLE.md) | Gemini |
| Hume | [HUME.md](HUME.md) | Hume Voice AI |
| Lovable | [LOVABLE.md](LOVABLE.md) | Lovable.dev |
| Manus | [MANUS.md](MANUS.md) | Manus AI |
| Meta | [META.md](META.md) | Llama, Muse |
| MiniMax | [MINIMAX.md](MINIMAX.md) | MiniMax AI |
| Mistral | [MISTRAL.md](MISTRAL.md) | Le Chat, Mistral |
| Moonshot | [MOONSHOT.md](MOONSHOT.md) | Kimi K2 |
| MultiOn | [MULTION.md](MULTION.md) | MultiOn (Browsing Agent) |
| OpenAI | [OPENAI.md](OPENAI.md) | ChatGPT, Codex, GPT-4o |
| Perplexity | [PERPLEXITY.md](PERPLEXITY.md) | Deep Research |
| Replit | [REPLIT.md](REPLIT.md) | Replit Agent |
| SameDev | [SAMEDEV.md](SAMEDEV.md) | Same.dev |
| Vercel | [VERCEL.md](VERCEL.md) | Vercel v0 |
| Windsurf | [WINDSURF.md](WINDSURF.md) | Windsurf (Codeium) |
| xAI | [XAI.md](XAI.md) | Grok |

## Como usar

Cada arquivo é autocontido e escrito para ser interpretado por qualquer agente ou LLM. Basta referenciar o caminho no prompt:

```
Siga as instruções em skills/PR.md para criar um pull request.
Use skills/AGENTS.md como guia geral de comportamento.
Use skills/ANTHROPIC.md como referência de comportamento do agente.
```

## Origem

Conteúdo adaptado do projeto CL4R1T4S (sistem prompts extraídos de IAs como OpenAI, Google, Anthropic, xAI, Perplexity, Cursor, Windsurf, Devin, Manus, Replit e mais).

## Licença

AGPL v3
