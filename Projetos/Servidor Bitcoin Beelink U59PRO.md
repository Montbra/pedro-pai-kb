# Projeto Servidor Bitcoin Caseiro - Beelink U59PRO

## Premissas Validadas

### Hardware Confirmado
- **Modelo**: Beelink U59PRO
- **RAM**: 16GB (excelente para todos os serviços)
- **Storage**: 1TB SSD (expansível para 2TB via BTRFS)
- **OS**: openSUSE Tumbleweed
- **Filesystem**: BTRFS (snapshots nativos para backup)

### Conectividade Definida
- **Rede Local**: Clearnet para sync rápido com nó existente
- **Acesso Remoto**: Apenas via Tor (sem abertura de portas no router)
- **SSH**: Chave Titan Google + hardening de segurança
- **Certificados**: Self-signed (suficiente para uso pessoal)
- **Anonimato**: Tor + i2p via containers

### Segurança Implementada
- **fail2ban**: Instalado (proteção contra brute force)
- **firewalld**: Ativo (controle de acesso)

### Estratégia de Backup
- **Escopo**: Problemas na máquina (não catástrofes)
- **Método**: BTRFS snapshots + backup interno na rede
- **Frequência**: Snapshots diários, backup semanal

## Plano de Implementação

### Fase 0: Hardening SSH + Firewall + fail2ban (30min - implementar depois)

#### fail2ban Configuration
```bash
# Instalação (já feita)
sudo zypper install fail2ban

# Configuração personalizada
sudo tee /etc/fail2ban/jail.d/bitcoin-server.conf << EOF
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3
backend = systemd

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
maxretry = 3
bantime = 7200

[nginx-http-auth]
enabled = true
port = http,https
logpath = /opt/bitcoin-stack/data/nginx/logs/error.log
maxretry = 3

[bitcoin-rpc]
enabled = true
port = 8332
logpath = /opt/bitcoin-stack/data/bitcoin/debug.log
failregex = ^.*Invalid authentication.*<HOST>.*$
maxretry = 5
bantime = 1800
EOF
```

#### SSH Security Enhancement
```bash
# /etc/ssh/sshd_config hardening
Protocol 2
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthenticationMethods publickey
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
AllowUsers bitcoin henrique
```

#### Titan Key Integration
```bash
# Configurar U2F/FIDO2 para SSH
sudo zypper install pam_u2f
# Registrar Titan key
pamu2fcfg > ~/.config/Yubico/u2f_keys
```

#### Firewall Rules
```bash
# firewalld configuração restritiva
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept'
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.0.0/8" service name="ssh" accept'
sudo firewall-cmd --reload
```

### Fase 1: Preparação Base (1-2 horas)
```bash
# Estrutura de diretórios
/opt/bitcoin-stack/
├── containers/          # Definições dos containers
├── data/               # Dados persistentes (1TB)
│   ├── bitcoin/        # ~500GB blockchain
│   ├── lightning/      # ~1GB channels/routing
│   ├── electrs/        # ~50GB índices
│   ├── tor/            # Dados Tor
│   ├── i2pd/           # Dados i2p
│   └── nginx/          # Logs nginx (fail2ban)
├── config/             # Configurações
├── scripts/            # Automação
└── compose/            # docker-compose files
```

**Tarefas:**
- [ ] Criar estrutura de diretórios
- [ ] Configurar Podman rootless
- [ ] Instalar podman-compose
- [ ] Configurar BTRFS subvolumes para snapshots
- [ ] Configurar firewall para containers
- [ ] Configurar fail2ban para containers

### Fase 2: Bitcoin Core - Sync Rápido (6-12 horas)
**Container Bitcoin:**
```yaml
# compose/bitcoin.yml
services:
  bitcoin:
    image: bitcoin/bitcoin:latest
    container_name: bitcoin-core
    ports:
      - "127.0.0.1:8332:8332"  # RPC
      - "8333:8333"            # P2P (rede local)
    volumes:
      - ../data/bitcoin:/bitcoin/.bitcoin
      - ../config/bitcoin.conf:/bitcoin/.bitcoin/bitcoin.conf:ro
    restart: unless-stopped
    logging:
      driver: journald
      options:
        tag: bitcoin-core
```

**bitcoin.conf otimizado:**
```ini
# Sync rápido via rede local
connect=<IP_DO_NO_EXISTENTE>:8333
addnode=<IP_DO_NO_EXISTENTE>:8333
dbcache=4000
maxconnections=20
prune=0
# Logs para fail2ban
debug=rpc
```

### Fase 3: Rede Anônima (1-2 horas)
```yaml
# compose/anonymity.yml
services:
  tor:
    image: osminogin/tor-simple
    container_name: tor-proxy
    ports:
      - "127.0.0.1:9050:9050"  # SOCKS proxy
      - "127.0.0.1:9051:9051"  # Control port
    volumes:
      - ../data/tor:/var/lib/tor
    restart: unless-stopped

  i2pd:
    image: purplei2p/i2pd
    container_name: i2pd-router
    ports:
      - "127.0.0.1:4444:4444"  # HTTP proxy
      - "127.0.0.1:4447:4447"  # SOCKS proxy
      - "127.0.0.1:7656:7656"  # SAM bridge
    volumes:
      - ../data/i2pd:/home/i2pd/data
      - ../config/i2pd:/home/i2pd/conf
    restart: unless-stopped
```

### Fase 4: Proxy Reverso + SSL (1-2 horas)
### Fase 5: Lightning Stack (3-4 horas)
### Fase 6: Operação e Backup (1-2 horas)

## Scripts de Segurança

### Configuração fail2ban Completa
```bash
# Script: scripts/configure-fail2ban.sh
#!/bin/bash
sudo tee /etc/fail2ban/jail.d/bitcoin-server.conf << EOF
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3
backend = systemd

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
maxretry = 3
bantime = 7200
EOF

sudo systemctl enable fail2ban
sudo systemctl restart fail2ban
sudo fail2ban-client status
```

---

**Versão**: 1.4 - Plano completo documentado  
**Data**: 2025-12-28  
**Status**: Pronto para implementação
