# SSHFS + Chave Titan - Cheatsheet

## Pré-requisitos
- Chave Titan conectada no USB
- SSH configurado em `~/.ssh/config`

## Configuração SSH (~/.ssh/config)
```
Host            bitcoin
Hostname        192.168.0.36
user            debian
IdentityFile    ~/.ssh/id_ecdsa_sk
IdentitiesOnly  yes
```

## Comandos Essenciais

### Montar servidor
```bash
mkdir -p ~/servidor-bitcoin
sshfs bitcoin: ~/servidor-bitcoin
```

### Desmontar
```bash
fusermount -u ~/servidor-bitcoin
```

### Verificar se está montado
```bash
mount | grep sshfs
```

### Montar com opções extras
```bash
# Com reconexão automática
sshfs bitcoin: ~/servidor-bitcoin -o reconnect

# Com cache para melhor performance
sshfs bitcoin: ~/servidor-bitcoin -o cache=yes

# Debug em caso de problemas
sshfs -o debug bitcoin: ~/servidor-bitcoin
```

## Uso no Dolphin
1. Monte com `sshfs bitcoin: ~/servidor-bitcoin`
2. Abra Dolphin
3. Navegue até `~/servidor-bitcoin`
4. Use normalmente como pasta local

## Acessando Diferentes Diretórios

### Montar diretórios específicos
```bash
# Diretório root (se tiver permissão)
sshfs bitcoin:/root ~/servidor-bitcoin-root

# Home de outro usuário
sshfs bitcoin:/home/outrouser ~/servidor-bitcoin-outrouser

# Diretórios do sistema
sshfs bitcoin:/var/log ~/servidor-bitcoin-logs
sshfs bitcoin:/etc ~/servidor-bitcoin-etc
```

### Múltiplos usuários SSH
Adicione ao `~/.ssh/config`:
```
Host            bitcoin-root
Hostname        192.168.0.36
user            root
IdentityFile    ~/.ssh/id_ecdsa_sk
IdentitiesOnly  yes
```

Então use: `sshfs bitcoin-root: ~/servidor-bitcoin-root`

### Operações como root (usuário com sudo)
```bash
# Copiar arquivo local para servidor como root
scp arquivo.txt bitcoin:/tmp/
ssh bitcoin "sudo mv /tmp/arquivo.txt /root/"

# Copiar do servidor como root
ssh bitcoin "sudo cp /root/arquivo.txt /tmp/ && sudo chown debian:debian /tmp/arquivo.txt"
scp bitcoin:/tmp/arquivo.txt ./
```

## Troubleshooting

### "device not found"
- Verifique se a chave Titan está conectada
- Teste primeiro: `ssh bitcoin "echo OK"`

### "Permission denied"
- Verifique configuração em `~/.ssh/config`
- Confirme que a chave pública está no servidor

### Desmontar forçado
```bash
sudo umount ~/servidor-bitcoin
# ou
fusermount -uz ~/servidor-bitcoin
```
