# Wi-Fi Power Save Fix

## Problema
Wi-Fi para de funcionar (sem desconectar) e precisa reconectar ou usar modo avião para voltar o tráfego.

## Causa
Power save ativado no adaptador Wi-Fi (MT7922 802.11ax).

## Solução Temporária
```bash
sudo /usr/sbin/iw dev wlo1 set power_save off
```

## Script Criado
Localização: `/home/henrique/.local/bin/disable-wifi-powersave.sh`

### Uso Manual
```bash
sudo ~/.local/bin/disable-wifi-powersave.sh
```

### Para Tornar Permanente

**Opção 1: NetworkManager Dispatcher**
```bash
sudo cp ~/.local/bin/disable-wifi-powersave.sh /etc/NetworkManager/dispatcher.d/99-disable-wifi-powersave
sudo chmod +x /etc/NetworkManager/dispatcher.d/99-disable-wifi-powersave
```

**Opção 2: Crontab**
```bash
sudo crontab -e
# Adicionar linha:
@reboot /home/henrique/.local/bin/disable-wifi-powersave.sh
```

## Verificar Status
```bash
sudo /usr/sbin/iw dev wlo1 get power_save
```
- `Power save: off` = OK
- `Power save: on` = Problema ativo
