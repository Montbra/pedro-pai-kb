# Relatório de Resolução: NVIDIA Xid 154 & Estabilidade (Lenovo Legion Slim 5)

## 💻 Contexto do Sistema
- **Hardware:** Lenovo Legion Slim 5 16AHP9 (Modelo 83DH)
- **GPU dGPU:** NVIDIA GeForce RTX 4070 Laptop (Ada Lovelace)
- **iGPU:** AMD Radeon 780M
- **OS:** openSUSE Tumbleweed
- **Kernel:** 6.19.x-default
- **Driver NVIDIA:** 580.142

---

## 🛑 Problemas Identificados
1.  **NVRM: Xid 154:** O driver NVIDIA entrava em colapso logo no boot (aprox. 14s), exigindo reboot ("Node Reboot Required").
2.  **Hang no Wayland:** A sessão Wayland "pendurava" ou congelava completamente ao tentar carregar a interface.
3.  **Atraso no X11:** A sessão X11 demorava minutos para abrir após a senha (causado pelo driver tentando se recuperar do Xid 154 em segundo plano).
4.  **Causa Raiz:** Instabilidade no firmware **GSP (GPU System Processor)** do driver 580.x ao lidar com a arquitetura Ada Lovelace e o gerenciamento de energia em sistemas híbridos.

---

## ✅ Solução Aplicada (Estabilidade Total e Blindagem)

### 1. Configuração do Driver (`/etc/modprobe.d/nvidia.conf`)
O arquivo foi configurado para eliminar tanto as falhas de firmware (Xid) quanto os congelamentos de energia (D3):

```bash
# 1. Desativa o firmware GSP (Solução definitiva para Xid 154)
options nvidia NVreg_EnableGpuFirmware=0

# 2. Desativa Dynamic Power Management D3 (Evita Hard Hangs/Congelamentos sob carga baixa)
options nvidia NVreg_DynamicPowerManagement=0x00

# 3. Ativa Modesetting e Buffer de Vídeo para Wayland/X11
options nvidia-drm modeset=1 fbdev=1

# 4. Preservação de VRAM (Evita travamentos em suspensão/resume)
options nvidia NVreg_PreserveVideoMemoryAllocations=1 NVreg_TemporaryFilePath=/var/tmp

# 5. Estabilidade de Energia, Áudio HDMI e Timeouts de Monitor
options nvidia NVreg_RegistryDwords="PowerMizerEnable=0x1; PerfLevelSrc=0x2222; PowerMizerLevel=0x3; PowerMizerDefault=0x3; PowerMizerDefaultAC=0x3; RM_EDID_TIMEOUT=1"
options snd_hda_intel power_save=0 power_save_controller=N
```

### 2. Atualização do Boot (Initramfs)
Sempre executado após mudanças no arquivo acima:
```bash
sudo dracut -f
```

### 3. Serviços de Suporte (Ativados)
Necessários para o correto funcionamento do Wayland e gerenciamento de energia:
- `nvidia-suspend.service`
- `nvidia-hibernate.service`
- `nvidia-resume.service`

---

## 🚀 Estratégia "Ouro" para LLM (Uso de Cabo USB-C) - VALIDADA

Para maximizar a VRAM disponível para modelos como o **Nemotron 30B**:

1.  **Conexão Física:** TV 4K conectada via cabo **USB-C (portas da esquerda)**.
    - *Nota Técnica:* A porta mais próxima da tela (frontal esquerda) foi validada como estável via `amdgpu` (`DP-2`).
2.  **BIOS:** Modo **Hybrid**.
3.  **Lógica Confirmada:**
    - A TV 4K é gerenciada pela **AMD**, aparecendo como `DP-2`.
    - O Desktop (KDE/Wayland/X11) roda na iGPU AMD.
    - A **RTX 4070** reporta apenas ~198 MiB de uso (overhead mínimo), deixando **~8.0 GB livres** para o `llama.cpp`.
    - Isso permite rodar modelos de 30B (Q4_K_M) ou 34B com muito mais camadas (NGL) na GPU.

---

## 🔍 Comandos de Verificação e Validação
- **Checar GSP Off:** `cat /proc/driver/nvidia/params | grep EnableGpuFirmware` (Deve ser 0)
- **Checar Xids no Log:** `sudo journalctl -k -b | grep -i "NVRM: Xid"`
- **Teste de Carga CUDA:** Validado com `test-backend-ops` em 07/04/2026.
  - *Resultado:* "found 1 CUDA devices... Device 0: NVIDIA GeForce RTX 4070... OK"
- **Ver VRAM Livre:** `nvidia-smi`

**Status Final:** Sistema Totalmente Estável (Wayland/X11/CUDA), sem Xids sob carga.
