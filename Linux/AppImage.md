
Preferencialmente criar novo diretório

_`${HOME}/AppImages`_

Criar um arquivo app.desktop em `${home}/.local/share/applications`. Ex com Obsidian:

```
[Desktop Entry]
Comment=
Exec=/home/henrique/AppImages/Obsidian-1.4.16.appimage
GenericName=Obsidian
Icon=/home/henrique/AppImages/obsidian.png
Name=Obsidian
NoDisplay=false
Path=
StartupNotify=true
Terminal=false
TerminalOptions=
Type=Application
X-KDE-SubstituteUID=false
X-KDE-Username=
```

Ícones com problema? Extrai aquivo com:

```
./your.AppImage --appimage-extract
```

Procurar ícones em _`squashfs-root`_

