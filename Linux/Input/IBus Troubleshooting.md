# IBus Troubleshooting - 30/01/2026

## Problema Inicial
- IBus estava crasheando (ibus-ui-gtk3 com SIGTRAP)
- Emojis e teclado funcionavam, mas não conseguia adicionar outros layouts
- Super+Espaço não alternava entre teclados
- Erro: `Process 2698 (ibus-ui-gtk3) of user 1000 dumped core`

## Diagnóstico Realizado

### 1. Verificação do Status
```bash
systemctl --user status ibus-wayland@autostart.service  # Serviço não encontrado
ps aux | grep ibus  # Processos rodando normalmente
ibus list-engine    # Engines disponíveis
ibus engine         # Engine atual: xkb:br::por
```

### 2. Configuração de Engines
```bash
gsettings get org.freedesktop.ibus.general engines-order  # Apenas ['xkb:br::por']
```

## Soluções Aplicadas

### 1. Adição de Múltiplos Engines
```bash
gsettings set org.freedesktop.ibus.general engines-order "['xkb:br::por', 'xkb:us::eng']"
gsettings set org.freedesktop.ibus.general preload-engines "['xkb:br::por', 'xkb:us::eng']"
```

### 2. Configuração de Hotkey
```bash
gsettings set org.freedesktop.ibus.general.hotkey triggers "['<Super>space']"
```

### 3. Reinicialização do IBus
```bash
ibus restart
```

### 4. Verificação de Conflitos com KDE
- Confirmado que KDE já estava configurado corretamente
- IBus e sistema nativo do KDE coexistindo sem conflitos
- Layout switching: `kxkbrc` com `Use=false` (IBus gerencia)

## Resultado Final
- **Super+Espaço** funcionando para alternar entre português e inglês
- IBus se recuperou automaticamente do crash
- Processo ibus-ui-gtk3 reiniciou automaticamente (PID 12084)
- Troca de engines via linha de comando funcionando:
  ```bash
  ibus engine xkb:us::eng  # Inglês
  ibus engine xkb:br::por  # Português
  ```

## Atualização Final
- **Comando executado**: `sudo zypper update ibus ibus-gtk3`
- Atualização disponível foi aplicada pelo usuário
- Correção definitiva do bug que causava crash do ibus-ui-gtk3

## Monitoramento Futuro
```bash
# Verificar logs de crashes
journalctl --user -u "*ibus*" --since "1 hour ago"

# Atualizar se necessário
sudo zypper update ibus ibus-gtk3
```

## Configurações Finais
- **Engines**: Português (BR) e Inglês (US)
- **Hotkey**: Super+Espaço
- **Modo**: Global switching via IBus
- **Sistema**: KDE Plasma com IBus integrado

## Atualização 01/02/2026 - Crash no Boot Persistente

### Status Atual
- **Problema**: Serviço `app-ibus-wayland@autostart.service` continua crasheando no boot
- **Erro**: `SIGTRAP` com mensagem "No input_method global"
- **Impacto**: NENHUM - IBus funciona normalmente após crash

### Processos Ativos (11:33 - 01/02/2026)
```
henrique    2302  ibus-ui-gtk3 --enable-wayland-im --exec-daemon
henrique    2353  ibus-daemon --xim --panel disable
henrique    2360  ibus-dconf
henrique    2361  ibus-extension-gtk3
henrique    2363  ibus-x11 --kill-daemon
henrique    2373  ibus-portal
henrique    2390  ibus-x11
henrique    2414  ibus-engine-simple
```

### Funcionalidades Confirmadas
- ✅ Amazon Q CLI com acentuação funcionando
- ✅ Super+Espaço alternando layouts
- ✅ Engine atual: `xkb:br::por`
- ✅ Engines configurados: português e inglês
- ✅ Todos os processos IBus ativos há 1+ hora

### Decisão de Manutenção
**MANTER CONFIGURAÇÃO ATUAL** - Não alterar nada que possa afetar o Amazon Q CLI.

### Monitoramento
```bash
# Log de status criado
echo "IBus Status: $(date)" >> ~/ibus-status.log
ps aux | grep ibus >> ~/ibus-status.log

# Verificar logs de crash
journalctl --user -u "*ibus*" --since "today"
```

### Observações Importantes
- O crash no autostart é cosmético - não afeta funcionalidade
- IBus se recupera automaticamente via outros mecanismos
- Amazon Q CLI (objetivo original) funcionando perfeitamente
- Configuração estável há semanas apesar dos crashes
