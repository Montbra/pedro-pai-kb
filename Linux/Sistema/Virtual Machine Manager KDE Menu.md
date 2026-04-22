# Virtual Machine Manager no Menu do KDE

## Problema
O Virtual Machine Manager não aparecia no menu de aplicativos do KDE, mesmo estando instalado.

## Causa
O arquivo `.desktop` original em `/usr/share/applications/virt-manager.desktop` continha a configuração `X-KDE-RootOnly=true`, que impedia sua exibição no menu normal de aplicações.

## Solução

1. **Verificar se está instalado:**
```bash
which virt-manager
```

2. **Criar arquivo .desktop local:**
```bash
mkdir -p ~/.local/share/applications
```

3. **Criar arquivo sem restrições de root:**
Arquivo: `~/.local/share/applications/virt-manager.desktop`
```ini
[Desktop Entry]
Name=Virtual Machine Manager
GenericName=Virtual machine viewer/manager
Comment=Manage Virtual Machines for Xen and KVM
Icon=virt-manager
Version=1.0
Exec=/usr/bin/virt-manager
Type=Application
Terminal=false
Categories=System;Emulator;
Keywords=virtualization;libvirt;vm;vmm;qemu;xen;lxc;kvm;
Encoding=UTF-8
```

4. **Atualizar cache de aplicações:**
```bash
update-desktop-database ~/.local/share/applications
```

## Resultado
O Virtual Machine Manager agora aparece no menu de aplicativos do KDE na categoria "Sistema".

## Se não funcionar
- Fazer logout/login
- Ou reiniciar o Plasma: `kquitapp5 plasmashell && kstart5 plasmashell`
