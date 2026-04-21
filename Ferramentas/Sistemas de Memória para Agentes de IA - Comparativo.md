# Sistemas de Memória para Agentes de IA — Comparativo (2026)

## Visão Geral

Comparativo dos principais sistemas de memória para agentes de IA em produção.

## 1. Holographic Memory (HRR)

- Fundado em Holographic Reduced Representations (Tony Plate, 1995)
- Representações composicionais em vetores de largura fixa via convolução circular
- Raciocínio algébrico: search, probe, reason, contradict
- No Hermes: SQLite local + trust scoring + algebraic fact store
- **Diferencial:** query composicional que cruza entidades (ex: "o que X prefere sobre Y no contexto Z?")
- **Trade-off:** requer estruturação consciente, não automático
- Paper original: Tony A. Plate (1995), "Holographic Reduced Representations"

## 2. Honcho

- Open source + serviço gerenciado
- Dialectic reasoning (agente "discute" com o que sabe sobre o usuário)
- peer.chat() retorna insights em linguagem natural, não apenas match
- Foco em deep user modeling
- Plugin para OpenClaw, funciona com qualquer framework
- **Diferencial:** modelagem profunda do usuário

## 3. OpenViking

- Context database como filesystem virtual
- LOD (Level of Detail) supply: carrega contextos granularmente
- Self-iteration: agente refina seu próprio context
- 11x redução de tokens, +15% task completion
- Unifica memory, resources e skills em um paradigma só
- **Diferencial:** economia de tokens + contexto granular

## 4. Hindsight

- Inspirado em Hindsight Experience Replay (HER) do RL
- Foco em agentes que aprendem com experiências passadas
- Diferencial: não é apenas recall — é learning from trajectory
- Transforma falhas em sucesso retroativamente
- **Diferencial:** aprendizado com erros passados

## 5. Mem0

- Universal memory layer, provider agnóstico
- 26% higher accuracy, 91% lower latency, 90% token savings
- Suporte a vector, semantic e raw memory
- Mais "plug-and-play" que os outros
- **Diferencial:** setup rápido, provider agnostic

## 6. Outros

- **ByteRover**: árvore de conhecimento em Markdown, inspecionável/editável
- **RetainDB**: híbrido keyword + semantic retrieval
- **Supermemory**: provedor alternativo
- **Zep**: similar ao Mem0, foco enterprise
- **Shodh-memory**: novo concorrente 2026

## Quando Usar o Quê?

| Use case | Melhor opção |
|---|---|
| Queries compostas (A sobre B no contexto C) | Holographic/HRR |
| Modelagem profunda de usuário | Honcho |
| Economia de tokens + contexto granular | OpenViking |
| Aprendizado com erros passados | Hindsight |
| Setup rápido, provider agnostic | Mem0 |
| Transparência total do que o agente sabe | ByteRover |

## Tipos de Memória em Agentes

- **Episódica:** log cronológico de interações, reprodutível
- **Semântica:** conhecimento distinto, queryable por similaridade
- **Procedural:** skills e adaptações aprendidas
- **Holographic:** representação algébrica com raciocínio composicional

---

**Tags:** #ia #memória #agentes #comparativo #holographic #honcho #mem0
**Data:** 2026-04-21
