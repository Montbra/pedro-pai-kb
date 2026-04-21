# Problema: Ollama e LM Studio não reconhecem GPU RTX 4070 Mobile

## Diagnóstico Completo

### Hardware
- **GPU:** NVIDIA GeForce RTX 4070 Laptop GPU (Compute Capability 8.9)
- **Sistema:** GPU híbrida (AMD + NVIDIA)
- **Driver NVIDIA:** 580.142 (Versão Março 2026)
- **CUDA suportado pelo driver:** 13.0

### Problemas Identificados e Resolvidos
1. **Xid 154 / Reboots:** Resolvido desativando o GSP Firmware (NVreg_EnableGpuFirmware=0).
2. **CUDA Error 999:** Contornado usando o backend Vulkan no llama.cpp nativo.
3. **Monitor HDMI Flicker:** Resolvido com offload parcial de camadas (NGL 20 no modelo 30B).

---

## O Setup de Ouro: Nemotron-Cascade 30B (26/03/2026)

### Configuração Estável
- **Modelo:** bartowski/nvidia_Nemotron-Cascade-2-30B-A3B-GGUF (Q4_K_M)
- **Backend:** Vulkan1 (NVIDIA RTX 4070)
- **Layers (NGL):** 20
- **Contexto:** 32k (com cache q4_0)
- **Performance:** 25.7 t/s

### Comando de Lançamento
```bash
/home/henrique/llama.cpp/build/bin/llama-server \
    -hf bartowski/nvidia_Nemotron-Cascade-2-30B-A3B-GGUF:nvidia_nemotron-cascade-2-30b-a3b-q4_k_m.gguf \
    --device Vulkan1 \
    -ngl 20 \
    -c 32768 \
    --cache-type-k q4_0 \
    --cache-type-v q4_0 \
    --flash-attn on \
    --port 8001 \
    --host 0.0.0.0
```

**Status:** 🚀 SISTEMA OPERACIONAL, ESTÁVEL E ALTAMENTE PERFORMÁTICO.
