
Preferencialmente criar novo diretório

_`${HOME}/AppImages`_

Criar um arquivo app.desktop em `${home}/.local/share/applications`. Ex com Minecraft:

```
[Desktop Entry]
Type=Application
Name=Obsidian
Comment=Obsidia
Icon=/home/henrique/AppImages/obsidian.png
Exec=/home/bram/Applications/Minecraft/minecraft
Terminal=false
Categories=Minecraft;game
```

Ícones com problema? Extrai aquivo com:

```
./your.AppImage --appimage-extract
```

Procurar ícones em _`squashfs-root`_

