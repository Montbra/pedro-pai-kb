# Relatório de Resolução de Crash de GPU - 16 de Março de 2026

## Sintomas Reportados
- **Reboots súbitos:** O sistema reiniciava sem aviso prévio ("do nada").
- **Congelamento (System Hang):** Travamento total da interface ("boom") durante uso leve (Firefox) ou após estresse de VRAM (LLM tests).
- **Ambiente:** Lenovo Legion 5 Slim (780M + RTX 4070), openSUSE Tumbleweed, KDE Plasma 6 (Wayland).

## Diagnóstico Técnico
1. **Loop de Erro no KWin:** Logs do sistema apresentavam falhas massivas de `GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT` no `kwin_wayland`.
2. **Regressão no Mesa 26.0.2:** O driver `radeonsi` (AMD) apresentou instabilidade na alocação de buffers para a arquitetura Phoenix/Hawk Point no protocolo EGL (Wayland).
3. **Falha de Sincronização Híbrida:** Erros ocorriam na tentativa de copiar o buffer da iGPU (AMD) para a dGPU (NVIDIA) via HDMI externo.
4. **Instabilidade de Energia:** O PSR (Panel Self Refresh) da AMD causava travamentos em estados ociosos.

## Ações Tomadas

### 1. Prevenção e Backup
- **Btrfs Snapshot:** Criado snapshot de segurança via Snapper (ID: **1273**) para permitir reversão total via menu do GRUB.

### 2. Ajustes de Kernel (Estabilidade de Hardware)
Foram adicionados os seguintes parâmetros ao `/etc/default/grub`:
- `amdgpu.sg_display=0`: Desativa o Scatter/Gather que causa crashes em chips Phoenix.
- `amdgpu.dcdebugmask=0x10`: Desativa o PSR (Panel Self Refresh) para evitar congelamentos em idle.
- `nvidia_drm.modeset=1`: Mantido para garantir sincronização correta da NVIDIA.

### 3. Mudança de Ambiente Gráfico
- **Migração para X11:** O sistema foi alterado de **Plasma (Wayland)** para **Plasma (X11)**. 
- **Resultado:** Os erros de framebuffer cessaram completamente e a NVIDIA assumiu como renderizador principal direto para o monitor externo, eliminando a ponte de buffers instável do Wayland.

### 4. Correção de Interface (HiDPI no X11)
Devido ao DPI de 288 (Scaling de 300% em 4K), o cursor no X11 ficou minúsculo. As seguintes correções foram aplicadas:
- Ajuste no `~/.Xresources` (`Xcursor.size: 48`).
- Ajuste no `~/.config/kcminputrc` via `kwriteconfig6`.
- Definição da variável de ambiente no `~/.pam_environment` (`XCURSOR_SIZE=48`) para persistência no login.

## Estado Final
- **Estabilidade:** Sem novos erros de framebuffer ou crashes registrados após a migração.
- **GPU:** NVIDIA RTX 4070 Laptop GPU operando como renderizador OpenGL.
- **Monitor Externo:** Operando em 4K @ 288 DPI com cursor em tamanho adequado.

## Recomendações Futuras
- **Mesa Update:** Testar o retorno ao Wayland somente após o Mesa ser atualizado para uma versão superior à 26.0.2 e conferir logs do KWin.
- **Reversão:** Caso deseje remover os parâmetros de kernel, edite `/etc/default/grub` e rode `sudo grub2-mkconfig -o /boot/grub2/grub.cfg`.
