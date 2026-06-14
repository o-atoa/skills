# VOZ — Interface de Voz e Comunicação Falada

Guia para projetar e operar interfaces de voz conversacionais para agentes de IA. Foco em naturalidade, clareza, empatia e adaptação cultural.

---

## 1. Projetando para Fala vs Texto

### Diferenças Fundamentais
- Fala é linear e transitória; texto é persistente e escaneável
- Fala tolera menos complexidade sintática
- Entonação substitui pontuação e formatação
- Erros de transcrição são esperados e devem ser tratados

### Gramática Conversacional
- Use contrações ("tô" em vez de "estou"), coordenação em vez de subordinação, marcadores discursivos ("então", "sabe", "tipo")

### Comprimento das Sentenças
- **7-15 palavras**: ideal | **Até 20**: aceitável | **25+**: divida em duas

### Breath Points e Pausas
- Pausa a cada 10-15 palavras
- Pause **antes** de info importante e **depois** de info complexa
- Pausa curta entre itens; pausa longa (1-2s) sinaliza transição

---

## 2. Emotional Granularity

### Espectro Emocional (Roda de Plutchik)
- **Êxtase**: admiração, espanto | **Alegria**: contentamento, gratidão, alívio, diversão
- **Confiança**: aceitação, serenidade | **Medo**: apreensão, ansiedade, preocupação
- **Surpresa**: distração, espanto | **Tristeza**: melancolia, pesar, decepção
- **Raiva**: irritação, frustração, indignação | **Nojo**: aversão, repulsa

### Matching Tom ao Contexto
- Frustração: valide → resolva ("puts, situação chata... deixa eu ver")
- Animação: espelhe energia ("nossa, que legal!")
- Indecisão: tom calmo ("sem pressa, vemos juntos")
- Tristeza: volume/velocidade baixos, validação silenciosa
- Urgência: direto, sem floreios

### Micro-expressões na Voz
- Tom ascendente → dúvida/pergunta | Tom descendente → afirmação
- Sussurro → confidencialidade | Ênfase repentina → novo insight | Suspiro → frustração

### Escada de Empatia (não pule degraus)
1. **Reconhecer** → 2. **Validar** → 3. **Apoiar** → 4. **Agir**
- "percebo que te incomodou" → "é compreensível" → "estou aqui" → "vou ajustar"

### Humor
- Autodepreciativo: só após erro, com moderação
- Trocadilho: contexto leve, usuário brincalhão
- Ironia/sarcasmo: apenas se usuário iniciou
- Regra: em dúvida, neutro. Nunca humor em contexto negativo.

---

## 3. Conversation Management

### Sinais de Turn-taking
- **Ceder**: tom ascendente + "né?"/"sabe?" | **Manter**: "então..." sustentado
- **Retomar**: "só mais uma coisa..." | **Oferecer**: pergunta direta

### Overlap e Interrupção
- Usuário falou por cima: pare, escute, retome ("como eu tava dizendo...")
- Interrupção com pergunta: responda, depois retome
- Após overlap: "fala aí" / "pode falar"

### Transições de Tópico
- Suave: "falando nisso..." / "por sinal..." | Explícita: "mudando de assunto..."
- Retorno: "como eu tava falando antes..." | Fechamento: "pra fechar esse ponto..."

### Gerenciamento de Silêncio
| Duração | Ação |
|---------|------|
| 0-2s | Espere — processamento |
| 2-4s | "hmm?" ou "quer que eu repita?" |
| 4-6s | "entendeu até aqui?" |
| 6s+ | Ofereça resumo ou mude abordagem |

Tarefas complexas: espere mais. Nunca interrompa reflexão com "alô?".

### Confirmação e Esclarecimento
- Aberta: "então é isso?" | Espelhada: "você quer dizer X?" | Mínimo: "como assim?"
- Guiado: "A ou B?" | Checkpoint: "só pra confirmar, temos X, Y e Z"

---

## 4. Repair Strategies

### Quando o Usuário Não Entendeu
- **Repetir** (distração), **Rephrase** (mesma info, outra estrutura), **Simplificar** (decompor), **Analogia** (conceito familiar), **Contextualizar** (conectar ao já dito)
- Não repita estratégia > 2x. Mude na terceira.

### Quando o Sistema Errou
1. Admita ("ops, errei") → 2. Corrija ("o correto é X") → 3. Desculpe-se uma vez → 4. Siga

### Ambiguidade
- Baixo risco: assuma provável e confirme | Alto risco: sempre confirme antes de agir

### Transcrição Imperfeita
- Contexto suficiente: corrija e prossiga | Afeta significado: "disse 15 ou 50?"
- Nome: soletre de volta | Incompreensível: "não peguei, pode repetir?"
- Nunca finja que entendeu — erros compostos são piores

### Restating vs Rephrasing vs Simplifying
- **Restating**: não ouviu → repita exato | **Rephrasing**: ouviu mas não processou → palavras diferentes
- **Simplifying**: informação densa → decomponha

---

## 5. Prosody and Pacing

### Entonação Natural
- Declarativa: tom descendente | Pergunta: ascendente | Enumeração: ascendente em cada item, descendente no último | Inciso: mais baixo e rápido | Contraste: ênfase + pausa

### Pausas para Ênfase
- 0.3-0.5s antes de palavra-chave | 0.5-1s depois de info importante | 1-2s entre tópicos | Zero em urgência

### Variação de Velocidade
- Rotina: normal-rápida | Complexa: 30-40% mais lenta | Urgência: 20-30% mais rápida | Empatia: 20-30% mais lenta

### Ênfase em Palavras-chave
- Números: sempre enfatizados | Contraste: forte ("não **amanhã**, é **hoje**") | Prazo: ênfase + pausa antes | Negação: no "não" e alternativa

---

## 6. Culture-Specific Communication

### Direta vs Indireta
- Direta (EUA, Alemanha): "Isso está errado" | Indireta (Brasil, Japão): "Talvez queira revisar"
- Brasil: críticas embrulhadas, recusas suaves ("vou dar uma olhada"), aproximação pessoal

### Níveis de Formalidade
- PT-BR: "você"/"senhor" | ESP: "tú"/"usted" | JPN: honoríficos (-san) | DEU: "du"/"Sie" | FRA: "tu"/"vous"
- Prefira formal até usuário sinalizar informalidade

### Expressões e Tabus
- Brasil: "jeitinho brasileiro", "resolveu?", "deu certo?" | Evite gírias muito regionais
- Tabus: religião, política (sempre), classe social, idade
- Regra: se não necessário, não aborde

---

## 7. Accessibility in Voice

### Articulação e Clareza
- Velocidade moderada padrão, finais de palavra pronunciados, dígitos individualizados em códigos ("nove, três, sete"), evite speech clipping

### Redundância para Info Importante
Padrão: afirmar → repetir → confirmar ("seu voo é amanhã, 15 de junho, 14h30. Dia 15, duas e meia.")
- Resumos periódicos. Repetição não é erro — é acessibilidade.

### Homófonos Problemáticos (PT-BR)
- cem/sem → "cem, número cem" | concerto/conserto → especifique | sessão/seção → "sessão de cinema" | acento/assento → "assento" | taxa/tacha → contexto

### Soletrando
- Nomes: "João — J de João, O, A, O" | Pares: "B de bola / P de pato"
- Números: agrupe "1-2-3" | CEPs/IDs: pares ou trios com pausa

### Acomodando Velocidades de Fala
- Rápido: acelere, menos pausas | Lento: desacelere, mais pausas
- Nunca complete frases do usuário ou apresse. Sinal de processamento: "hmm" / "deixa eu ver"

### Outras Práticas
- Evite "ele"/"ela" sem contexto | Datas: "15 de junho" | Horas: "duas e meia"
- Listas: "primeiro... segundo..." | Use "o(a) senhor(a)" em contextos formais

---

## 8. Multi-Turn Conversations

### Manutenção de Contexto
- Referencie o passado ("como você mencionou..."), resuma periodicamente ("só pra alinhar..."), histórico de preferências, pronomes consistentes, retome último tópico após pausa

### Sumarização
- Checkpoint a cada 5-7 turnos | Antes de mudar: "fechando esse tópico..." | Nova sessão: "quer continuar de onde parou?"

### Re-engajamento
- Dentro do turno: "está acompanhando?" | Usuário volta: "bem-vindo de volta!" | Novo dia: "quer continuar ou é outra coisa?"

### Confirmação de Conclusão
- "pronto, resolvido!" / "quer que eu repita?" | Check-in: "alguma dúvida?" | Fechamento: "se surgir algo, me chama"

### Aprendizado de Preferências
- Implícitas: note e confirme | Explícitas: memorize | Permita reverter: "mudou de ideia?"

### Padrão de Fechamento
1. Confirmação → 2. Resumo → 3. Abertura → 4. Despedida

---

## Quick Reference

### Checklist
- Sentenças 7-15 palavras, máx 1 pergunta/resposta, abertura < 5 palavras
- Breath point a cada 10-15 palavras, tom espelha contexto, homófonos desambiguados
- Contrações, pausas para ênfase, repair honesto, resumo periódico

### Sinais de Alerta
- Usuário repetiu mesma info 2x, "não" à confirmação > 1x, silêncio > 4s, pediu simplificação, incoerência tom/conteúdo

### Frases de Emergência
| Situação | Frase |
|----------|-------|
| Não entendeu | "Perdão, não peguei. Pode repetir?" |
| Erro do sistema | "Ops, erro meu. Deixa eu corrigir." |
| Precisa de tempo | "Deixa eu pensar rapidinho..." |
| Mudar tópico | "Mudando de assunto..." |
| Fechar tópico | "Então, fechamos isso?" |
