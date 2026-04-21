# Btrfs Recovery Cheatsheet

## Verificação Segura (Filesystem Montado)

```bash
# Iniciar scrub (recomendado)
sudo btrfs scrub start /mount/point

# Verificar progresso
sudo btrfs scrub status /mount/point

# Monitorar em tempo real
watch -n 2 'sudo btrfs scrub status /mount/point'

# Pausar/cancelar scrub
sudo btrfs scrub cancel /mount/point

# Retomar scrub
sudo btrfs scrub resume /mount/point
```

## Verificação Básica (Filesystem Desmontado)

```bash
# Desmontar primeiro (SEMPRE recomendado)
sudo umount /mount/point

# Verificação somente leitura
sudo btrfs check /dev/sdX

# Verificação com checksums
sudo btrfs check --check-data-csum /dev/sdX
```

## Reparo (CUIDADO - Risco de Perda de Dados)

```bash
# Reparo automático
sudo btrfs check --repair /dev/sdX

# Recuperação de superbloco
sudo btrfs rescue super-recover /dev/sdX
```

## Montagem de Emergência

```bash
# Montar somente leitura
sudo mount -o ro,recovery /dev/sdX /mount/point

# Remontar como somente leitura
sudo mount -o remount,ro /mount/point

# Voltar para leitura/escrita
sudo mount -o remount,rw /mount/point
```

## Informações do Sistema

```bash
# Status geral
sudo btrfs filesystem show
sudo btrfs filesystem usage /mount/point

# Logs do sistema
dmesg | grep -i btrfs
journalctl -u systemd-fsck@dev-sdX.service
```

## ⚠️ Avisos Importantes

- **NUNCA** use `btrfs check --force` em filesystem montado
- **SEMPRE** faça backup antes de reparos
- **PREFIRA** `btrfs scrub` para verificações em filesystems montados
- **DESMONTE** sempre que possível antes de usar `btrfs check`

## Gerenciamento de Espaço (Unallocated Space)

```bash
# Verificar espaços alocados vs não alocados
sudo btrfs filesystem usage /
sudo btrfs filesystem show

# Liberar espaço não alocado (resolve "No space left")
sudo btrfs balance start -dusage=50 /

# Rebalanceamento completo
sudo btrfs balance start /
```

### ⚠️ Importância do Espaço Não Alocado
- **Problema**: "No space left on device" mesmo com espaço livre
- **Causa**: Falta de espaço não alocado para operações do Btrfs
- **Solução**: Manter 10-20% de espaço não alocado
- **Necessário para**: Rebalanceamento, conversões RAID, operações internas

### 🆘 Limpeza de Emergência - Liberar Espaço

#### 🔥 Prioridade ALTA (Mais seguro)
```bash
# Logs do sistema
sudo journalctl --vacuum-time=1d
sudo find /var/log -name "*.log" -mtime +7 -delete
sudo find /var/log -name "*.gz" -delete

# Cache de pacotes
sudo apt clean              # Debian/Ubuntu
sudo zypper clean --all     # openSUSE
sudo dnf clean all          # Fedora

# Arquivos temporários
sudo rm -rf /tmp/*
sudo rm -rf /var/tmp/*
sudo find /var/cache -type f -delete
```

#### 🟡 Prioridade MÉDIA (Verificar antes)
```bash
# Cache do usuário
rm -rf ~/.cache/*
rm -rf ~/.thumbnails/*

# Downloads antigos
find ~/Downloads -mtime +30 -type f

# Lixeira
rm -rf ~/.local/share/Trash/*

# Snapshots antigos do Btrfs
sudo btrfs subvolume list / | grep snapshot
# sudo btrfs subvolume delete /.snapshots/XXX
```

#### 📊 Encontrar arquivos grandes
```bash
# Top 20 maiores arquivos
sudo find / -type f -size +100M 2>/dev/null | head -20
sudo du -ah / 2>/dev/null | sort -rh | head -20

# Por diretório
sudo du -sh /* 2>/dev/null | sort -rh
```

**Ordem de limpeza**: Logs → Cache → Temp → Thumbnails → Downloads → Snapshots

### 🚨 Medida Extrema - Adicionar Pendrive Temporário

#### Passo 1: Identificar o pendrive
```bash
# Ver todos os dispositivos ANTES de plugar o pendrive
lsblk

# Plugar o pendrive e executar novamente
lsblk

# Identificar o novo dispositivo (ex: /dev/sdb, /dev/sdc)
# Confirmar tamanho e nome para ter certeza
sudo fdisk -l | grep -A5 "Disk /dev/sd"
```

#### Passo 2: Preparar o pendrive (se necessário)
```bash
# Se o pendrive tiver partições, desmonte primeiro
sudo umount /dev/sdX1  # substitua X pela letra correta

# OPCIONAL: Limpar partições existentes (APAGA TUDO!)
# sudo wipefs -a /dev/sdX
```

#### Passo 3: Adicionar ao pool Btrfs
```bash
# Adicionar pendrive ao pool (substitua sdX pelo dispositivo correto)
sudo btrfs device add /dev/sdX /

# Verificar se foi adicionado com sucesso
sudo btrfs filesystem show
sudo btrfs filesystem usage /
```

#### Passo 4: Redistribuir dados
```bash
# Fazer balance para usar o novo espaço
sudo btrfs balance start -dusage=1 /

# Monitorar progresso
sudo btrfs balance status /
```

#### Passo 5: Remover pendrive (IMPORTANTE!)
```bash
# DEPOIS de resolver o problema, remover o pendrive
sudo btrfs device remove /dev/sdX /

# Confirmar remoção
sudo btrfs filesystem show
```

⚠️ **CUIDADOS CRÍTICOS**:
- **CONFIRME** o dispositivo correto com `lsblk` antes de adicionar
- Use pendrive com pelo menos 4-8GB livres
- **NUNCA** remova o pendrive fisicamente enquanto estiver no pool
- **SEMPRE** execute `btrfs device remove` antes de desplugá-lo
- Não desligue o sistema com pendrive no pool

## Ordem de Prioridade

1. **Primeiro**: `btrfs scrub` (filesystem montado)
2. **Segundo**: `btrfs check` (filesystem desmontado)
3. **Último recurso**: `btrfs check --repair` (com backup!)
