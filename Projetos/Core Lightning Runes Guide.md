# Core Lightning Runes - Guia Completo

## O que são Runes?

Runes no Core Lightning são um sistema flexível de autorização que permite controle de acesso granular para comandos RPC. São essencialmente tokens portadores que codificam permissões e restrições.

## Estrutura dos Runes

Um rune consiste em:
- **ID Único**: Identifica o rune específico
- **Restrições**: Lista de condições que devem ser atendidas
- **Assinatura**: Prova criptográfica de autenticidade

## Criando Runes

```bash
# Criar rune que permite apenas comando 'getinfo'
lightning-cli createrune restrictions='[["method=getinfo"]]'

# Criar rune com múltiplas restrições
lightning-cli createrune restrictions='[["method=invoice"],["method=listinvoices"],["time<1704067200"]]'

# Criar rune com limite de taxa (máx 10 chamadas por minuto)
lightning-cli createrune restrictions='[["rate=10"]]'
```

## Tipos Comuns de Restrições

- **method**: Restringir a métodos RPC específicos (`method=getinfo`)
- **time**: Expiração baseada em tempo (`time<1704067200`)
- **id**: Restringir a IDs de nó específicos (`id=nodeID`)
- **rate**: Limite de taxa (`rate=10` para 10 chamadas por minuto)
- **per**: Janela de tempo para limite de taxa (`per=60` para por minuto)
- **pnum**: Restrições de número de parâmetro
- **pnameXXX**: Restrições de nome de parâmetro

## Usando Runes

```bash
# Usar rune para autenticação
lightning-cli --rune="sua_string_rune_aqui" getinfo

# Em chamadas API, incluir no cabeçalho Authorization
curl -H "Authorization: Bearer sua_string_rune" http://localhost:9735/v1/getinfo
```

## Gerenciando Runes

```bash
# Listar todos os runes
lightning-cli listrunes

# Verificar se um rune é válido
lightning-cli checkrune rune="sua_string_rune"

# Destruir um rune
lightning-cli destroyrune rune_id
```

## Benefícios de Segurança

- **Princípio do menor privilégio**: Conceder apenas permissões necessárias
- **Acesso limitado por tempo**: Expiração automática
- **Revogável**: Pode ser destruído quando não precisar mais
- **Auditável**: Rastrear uso e permissões
- **Sem segredos compartilhados**: Cada rune é único

## Casos de Uso Exemplo

1. **Acesso somente leitura**: `method=getinfo|method=listchannels`
2. **Criação de faturas**: `method=invoice` com limites de valor
3. **Acesso temporário**: Runes com restrição de tempo para operações específicas
4. **Integração API**: Runes com limite de taxa para serviços externos

---

## Integração com RTL (Ride The Lightning)

RTL pode usar runes em vez de macaroons para autenticação com nós Core Lightning.

### Configuração no RTL

No arquivo de configuração do RTL (`RTL-Config.json`):

```json
{
  "nodes": [
    {
      "index": 1,
      "lnNode": "Core Lightning",
      "lnImplementation": "CLN",
      "Authentication": {
        "runePath": "/caminho/para/arquivo/rune",
        "runeValue": "sua_string_rune_aqui"
      },
      "Settings": {
        "userPersona": "OPERATOR",
        "themeMode": "DAY",
        "themeColor": "PURPLE",
        "fiatConversion": false,
        "logLevel": "INFO",
        "lnServerUrl": "https://127.0.0.1:3001/v1"
      }
    }
  ]
}
```

### Criando Runes Compatíveis com RTL

```bash
# Rune de acesso completo para RTL (não recomendado para produção)
lightning-cli createrune

# Rune restrito para operações RTL
lightning-cli createrune restrictions='[
  ["method=getinfo"],
  ["method=listfunds"],
  ["method=listchannels"],
  ["method=listpeers"],
  ["method=invoice"],
  ["method=pay"],
  ["method=listinvoices"],
  ["method=listpays"]
]'
```

### Requisitos de Permissão do RTL

RTL precisa de acesso a estes métodos do Core Lightning:
- `getinfo` - Informações do nó
- `listfunds` - Saldo da carteira
- `listchannels` - Gerenciamento de canais
- `listpeers` - Conexões de peers
- `invoice` - Criar faturas
- `pay` - Fazer pagamentos
- `listinvoices` - Histórico de faturas
- `listpays` - Histórico de pagamentos
- `connect` - Conectar a peers
- `fundchannel` - Abrir canais

### Configuração de Produção Exemplo

```bash
# Criar rune restrito para RTL com expiração de 30 dias
lightning-cli createrune restrictions='[
  ["method=getinfo|method=listfunds|method=listchannels|method=listpeers"],
  ["method=invoice|method=pay|method=listinvoices|method=listpays"],
  ["time<'$(date -d '+30 days' +%s)'"]
]'
```

### Benefícios de Segurança com RTL

Usar runes com RTL oferece:
- **Permissões granulares**: Limitar RTL apenas a operações necessárias
- **Expiração baseada em tempo**: Expiração automática de token
- **Acesso revogável**: Destruir runes quando necessário
- **Trilha de auditoria**: Rastrear uso da API do RTL

Esta abordagem substitui a autenticação tradicional baseada em macaroons, oferecendo controle de acesso mais flexível e seguro para a interação do RTL com seu nó Core Lightning.
