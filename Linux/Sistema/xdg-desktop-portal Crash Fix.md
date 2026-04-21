# xdg-desktop-portal Crash Fix

## Problema
O processo `xdg-desktop-portal` estava crashando repetidamente com:
- **Erro**: `g_object_ref: assertion 'G_IS_OBJECT (object)' failed`
- **Signal**: 11 (SEGV - segmentation fault)
- **Causa**: Tentativa de referenciar um objeto GObject inválido/NULL durante chamada D-Bus

## Solução Aplicada
Executar atualização completa do sistema (distribution upgrade):

```bash
sudo zypper ref
sudo zypper dup --allow-vendor-change --auto-agree-with-licenses
```

### Por que --allow-vendor-change?
Você tem múltiplos repositórios (openSUSE oficial, X11:XOrg, Packman, NVIDIA) e alguns pacotes precisam mudar de vendor durante a atualização. Isso é normal no Tumbleweed com repositórios adicionais.

## Soluções Alternativas (se o update não resolver)

### 1. Limpar cache do xdg-desktop-portal
```bash
rm -rf ~/.cache/xdg-desktop-portal
rm -rf ~/.local/share/xdg-desktop-portal
systemctl --user restart xdg-desktop-portal.service
```

### 2. Verificar backends disponíveis
```bash
ls -la /usr/share/xdg-desktop-portal/portals/
```

### 3. Forçar uso do backend KDE
```bash
mkdir -p ~/.config/xdg-desktop-portal
cat > ~/.config/xdg-desktop-portal/portals.conf << 'EOF'
[preferred]
default=kde
org.freedesktop.impl.portal.Screenshot=kde
org.freedesktop.impl.portal.FileChooser=kde
org.freedesktop.impl.portal.Settings=kde
EOF
```

### 4. Reinstalar pacotes relacionados
```bash
sudo zypper install --force-resolution xdg-desktop-portal xdg-desktop-portal-kde
```

### 5. Workaround temporário (desabilitar o serviço)
```bash
systemctl --user mask xdg-desktop-portal.service
```

## Verificar após reboot
```bash
# Ver status do serviço
systemctl --user status xdg-desktop-portal.service

# Ver logs
journalctl --user -u xdg-desktop-portal.service -b

# Ver crashes recentes
coredumpctl list
```

## Contexto Técnico
- O crash acontece no thread que faz chamadas D-Bus (`g_dbus_proxy_call_sync`)
- Stack trace mostra envolvimento de libgio-2.0, libglib-2.0, libpipewire e libdconfsettings
- Problema comum em Tumbleweed após atualizações parciais de bibliotecas GLib/GIO
- A atualização completa (zypper dup) sincroniza todas as bibliotecas

## Data
2026-03-08 17:23
