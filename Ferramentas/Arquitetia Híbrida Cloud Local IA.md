# Arquitetura Híbrida: Cloud (Estratégia) + Local (Execução)

## Visão Geral

Este projeto implementa uma arquitetura onde:
- **Modelos SOTA na Cloud** (Claude/GPT): fazem planejamento estratégico e análise de alto nível
- **Modelos Locais** (Qwen via Ollama): executam tarefas operacionais de codificação

## Stack

| Camada | Ferramenta | Modelo | Custo |
|--------|-----------|--------|-------|
| **Estratégia** | Anthropic API | Claude Sonnet/Opus | ~$3-15/1M tokens |
| **Execução** | Ollama | Qwen2.5-Coder-14B | Free |
| **Orquestração** | Python + OpenHands | - | Free |

## Hardware Alvo

- GPU: NVIDIA RTX 4070 Laptop (8GB VRAM)
- RAM: 32GB
- CPU: Ryzen 7 8845HS (16 núcleos)

## Instalação

### 1. Ollama (já instalado)
```bash
# Baixar modelo Qwen2.5-Coder
ollama pull qwen2.5-coder:14b

# Testar
ollama run qwen2.5-coder:14b "Hello!"
```

### 2. Ambiente Python
```bash
python3 -m venv venv-rabbitllm
source venv-rabbitllm/bin/activate
pip install rabbitllm
```

## Estrutura de Arquivos

```
.
├── src/
│   ├── planner.py        # Integração com Claude (estratégia)
│   ├── executor.py       # Integração com Ollama (execução)
│   └── orchestrator.py   # Orquestra planner + executor
├── examples/
│   └── create_api.py     # Exemplo de caso de uso
├── tests/
├── venv-rabbitllm/
└── README.md
```

## Casos de Uso

### 1. Desenvolvimento de Features
```
Claude (Cloud):
  - Analisa requisitos
  - Planeja arquitetura da feature
  - Decompose em tarefas

Qwen Local (Ollama):
  - Gera código boilerplate
  - Implementa funções específicas
  - Cria testes unitários
```

### 2. Automação com OpenHands
```
Claude (Cloud):
  - Define objetivo de alto nível
  - Revisa progresso

OpenHands + Qwen Local:
  - Executa comandos no terminal
  - Edita arquivos
  - Roda testes
```

## Próximos Passos

1. [ ] Configurar GPU para Ollama (atualmente em CPU)
2. [ ] Criar script orchestrator.py
3. [ ] Integrar com API da Anthropic
4. [ ] Testar caso de uso real (criar API REST)
