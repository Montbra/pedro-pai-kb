# Laravel Filament Best Practices Skill

```yaml
name: laravel-filament-best-practices
version: 1.0.0
description: Guia completo para criação de aplicações Laravel Filament com foco em código limpo, modularidade, segurança, testabilidade, escalabilidade e CI/CD automatizado com custos otimizados.
author: Kilo AI
tags: [laravel, filament, php, best-practices, architecture, security, testing, ci-cd]
```

---

## OBJETIVO

Criar uma aplicação Laravel com Filament PHP seguindo as melhores práticas de mercado, com foco em código limpo, modularidade, segurança, testabilidade, escalabilidade e CI/CD automatizado com custos otimizados.

---

## 1. ARQUITETURA E ESTRUTURA

### Estrutura Modular (Domain-Driven Design)

Adotar arquitetura modular com modules/ ou usar pacote `nwidart/laravel-modules`. Cada módulo deve conter: Domain, Application, Infrastructure, Presentation. Separação clara de responsabilidades seguindo princípios SOLID.

### Camadas Recomendadas

```
app/
├── Domains/
│   ├── {Domain}/
│   │   ├── Models/
│   │   ├── Repositories/
│   │   ├── Services/
│   │   ├── Events/
│   │   ├── Listeners/
│   │   └── Exceptions/
├── Application/
│   ├── UseCases/
│   ├── DTOs/
│   └── Validators/
├── Infrastructure/
│   ├── Repositories/
│   ├── ExternalServices/
│   └── Persistence/
└── Presentation/
    ├── Filament/
    │   ├── Resources/
    │   ├── Pages/
    │   └── Widgets/
    └── Http/
```

---

## 2. CÓDIGO E BOAS PRÁTICAS

### Padrões a Seguir

- **PSR-12** para estilo de código
- **Repository Pattern** para abstração de dados
- **Service Layer** para lógica de negócio
- **Action Classes** para operações únicas (single responsibility)
- **DTOs** para transferência de dados entre camadas
- **Enums tipados** para valores fixos
- **Strict types** em todos os arquivos PHP: `declare(strict_types=1);`

### Ferramentas de Qualidade

| Ferramenta | Finalidade |
|------------|------------|
| Laravel Pint | Formatação de código |
| PHPStan / Larastan | Análise estática (nível 8+) |
| Rector | Refatoração automática |
| PHPMD | Detecção de code smells |

---

## 3. SEGURANÇA

### Implementações Obrigatórias

- [ ] **Authentication**: Laravel Sanctum/Passport ou Filament native auth
- [ ] **Authorization**: Policies + Gates + Filament Permissions (spatie/laravel-permission)
- [ ] **Input Validation**: Form Requests + Filament Forms validation
- [ ] **SQL Injection Prevention**: Eloquent ORM + prepared statements
- [ ] **XSS Protection**: Blade escaping + sanitização de inputs
- [ ] **CSRF Protection**: Native Laravel CSRF
- [ ] **Rate Limiting**: Laravel Rate Limiting + Filament middleware
- [ ] **Audit Log**: spatie/laravel-activitylog
- [ ] **Encryption**: Laravel Encryption para dados sensíveis
- [ ] **Headers de Segurança**: CSP, HSTS, X-Frame-Options
- [ ] **Environment Isolation**: .env segregados por ambiente

### Pacotes Recomendados

```
spatie/laravel-permission
spatie/laravel-activitylog
laravel/sanctum
bliss/paranoid (opcional)
```

---

## 4. TESTES

### Estrutura de Testes

```
tests/
├── Unit/
│   ├── Services/
│   ├── Repositories/
│   └── DTOs/
├── Feature/
│   ├── Http/
│   ├── Filament/
│   └── Integration/
└── Architecture/ (using Pest or PHPStan)
```

### Ferramentas de Teste

| Ferramenta | Finalidade |
|------------|------------|
| PHPUnit OU Pest PHP | Testes unitários e de feature (preferência: Pest) |
| Livewire Testing | Testes de componentes Filament |
| Laravel Dusk | Testes E2E críticos |
| Mockery | Mocks e stubs |
| Faker | Fixtures e dados fake |

### Cobertura Mínima

- **Unit Tests**: 80%+ coverage
- **Feature Tests**: Cenários críticos de negócio
- **Integration Tests**: APIs e fluxos Filament

### Exemplo de Teste Pest com Filament

```php
use function Pest\Livewire\livewire;

it('can list users', function () {
    $users = User::factory()->count(10)->create();
    
    livewire(UserResource\Pages\ListUsers::class)
        ->assertCanSeeTableRecords($users);
});

it('can create user', function () {
    $data = User::factory()->make()->toArray();
    
    livewire(UserResource\Pages\CreateUser::class)
        ->fillForm($data)
        ->call('create')
        ->assertHasNoFormErrors();
    
    $this->assertDatabaseHas('users', [
        'email' => $data['email'],
    ]);
});
```

---

## 5. ESCALABILIDADE E PERFORMANCE

### Cache

- Redis para cache de aplicação
- Cache de queries frequentes
- Cache de configurações
- Model caching (opcional: `genealabs/laravel-model-caching`)

### Queue & Jobs

- Redis/Database queues
- Horizon para monitoramento
- Job batching para operações pesadas
- Rate limiting em jobs

### Database

- Read replicas configuradas
- Indexação otimizada
- Query optimization com eager loading
- Soft deletes para dados críticos
- Partitioning para tabelas grandes

### Otimizações

| Técnica | Descrição |
|---------|-----------|
| Octane | Alta performance com Swoole/RoadRunner |
| OPcache | Habilitado em produção |
| Composer autoload | Otimização de autoload |
| Vite | Asset bundling eficiente |

---

## 6. DISPONIBILIDADE

### Estratégias

- Health checks endpoint (`/health`)
- Graceful shutdown
- Circuit breaker para serviços externos
- Fallbacks para operações críticas
- Database connection pooling

### Monitoramento

| Ferramenta | Finalidade |
|------------|------------|
| Laravel Telescope | Debug dev/staging |
| Sentry/Bugsnag | Error tracking |
| Prometheus + Grafana | Métricas e dashboards |
| ELK Stack ou Loki | Log aggregation |

---

## 7. CI/CD E DEPLOY AUTOMATIZADO

### Pipeline GitLab CI/CD

```yaml
stages:
  - test
  - build
  - deploy

variables:
  PHP_VERSION: "8.2"

test:unit:
  stage: test
  image: php:$PHP_VERSION-cli
  script:
    - composer install --prefer-dist --no-interaction
    - cp .env.testing .env
    - php artisan key:generate
    - ./vendor/bin/pest --parallel
  coverage: '/^\s*Lines:\s*\d+.\d+\%/'

test:static:
  stage: test
  script:
    - ./vendor/bin/phpstan analyse --memory-limit=2G

test:security:
  stage: test
  script:
    - ./vendor/bin/security-checker security:check

build:image:
  stage: build
  script:
    - docker build -t registry/app:$CI_COMMIT_SHA .
    - docker push registry/app:$CI_COMMIT_SHA
  only:
    - main
    - develop

deploy:production:
  stage: deploy
  script:
    - kubectl set image deployment/app app=registry/app:$CI_COMMIT_SHA
  environment: production
  only:
    - main
```

### Pipeline GitHub Actions

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
          extensions: mbstring, xml, mysql, pgsql, redis
      
      - name: Install Dependencies
        run: composer install --prefer-dist --no-interaction
      
      - name: Execute Tests
        run: ./vendor/bin/pest --parallel --coverage
      
      - name: Static Analysis
        run: ./vendor/bin/phpstan analyse

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to Production
        run: |
          # Deploy script here
```

### Infraestrutura como Código

| Ferramenta | Uso |
|------------|-----|
| Terraform | Provisionamento de infraestrutura |
| Docker + Docker Compose | Desenvolvimento local |
| Kubernetes | Orquestração em produção |
| Helm Charts | Deployments K8s |
| Laravel Vapor | Serverless (opcional) |

---

## 8. CUSTOS OTIMIZADOS

### Estratégias de Otimização

#### Infraestrutura

| Estratégia | Descrição |
|------------|-----------|
| Serverless | Workloads esporádicas (AWS Lambda / Vapor) |
| Spot Instances | Ambientes não-críticos |
| Auto-scaling | Baseado em métricas reais |
| Reserved Instances | Workloads previsíveis |
| CDN | CloudFront/Cloudflare para assets |

#### Database

- RDS Proxy para connection pooling
- Multi-AZ apenas se necessário SLA crítico
- Read replicas sob demanda
- S3 para arquivos grandes (evitar BLOB)

#### Application

- Lazy loading de relacionamentos Filament
- Pagination em todas as listagens
- Deferred imports em Filament
- Queue para operações pesadas
- Cache agressivo de dados imutáveis

#### Monitoring de Custos

- AWS Cost Explorer
- CloudWatch Billing Alarms
- Resource tagging obrigatório
- Regular cost reviews

---

## 9. MANUTENÇÃO

### Documentação

| Documento | Descrição |
|-----------|-----------|
| OpenAPI/Swagger | Documentação de APIs |
| README.md | Por módulo |
| CHANGELOG.md | Keep a Changelog format |
| ADRs | Architecture Decision Records |

### Versionamento

- **Semantic Versioning** (SemVer)
- **Conventional Commits**
- **Branch naming**: `feature/`, `fix/`, `hotfix/`, `release/`

### Deprecation Policy

- Avisos de deprecation em logs
- Versões suportadas documentadas
- Migration guides para breaking changes

---

## 10. FILAMENT-SPECIFIC BEST PRACTICES

### Resources

- Usar Resource classes separadas por contexto
- Custom Pages para fluxos complexos
- RelationManagers para relacionamentos
- Widgets para dashboards
- Form Builder com componentes reutilizáveis
- Table Builder com actions customizadas

### Performance Filament

| Técnica | Implementação |
|---------|---------------|
| Lazy loading | Relacionamentos |
| Bulk actions | Otimizadas |
| Cached navigation | Configurar cache |
| Deferred loading | Widgets |
| Eager loading | Queries otimizadas |

### Plugins Úteis

```
filament/spatie-laravel-media-library-plugin
filament/spatie-laravel-tags-plugin
filament/spatie-laravel-settings-plugin
bezhansalleh/filament-shield
pxlrbt/filament-excel
filament/filament-logger
```

### Exemplo de Resource Otimizado

```php
<?php

declare(strict_types=1);

namespace App\Filament\Resources;

use App\Filament\Resources\UserResource\Pages;
use App\Models\User;
use Filament\Resources\Resource;
use Filament\Tables\Table;

class UserResource extends Resource
{
    protected static ?string $model = User::class;
    
    protected static ?string $navigationIcon = 'heroicon-o-users';
    
    protected static ?int $navigationSort = 1;
    
    public static function table(Table $table): Table
    {
        return $table
            ->columns([
                // columns
            ])
            ->filters([
                // filters
            ])
            ->actions([
                // actions
            ])
            ->bulkActions([
                // bulk actions
            ])
            ->defaultPaginationPageOption(25)
            ->deferLoading();
    }
    
    public static function getRelations(): array
    {
        return [
            // RelationManagers
        ];
    }
    
    public static function getPages(): array
    {
        return [
            'index' => Pages\ListUsers::route('/'),
            'create' => Pages\CreateUser::route('/create'),
            'edit' => Pages\EditUser::route('/{record}/edit'),
        ];
    }
}
```

---

## 11. COMANDOS DE SETUP INICIAL

```bash
# Criar projeto
laravel new app --stack=livewire

# Instalar Filament
composer require filament/filament:"^3.0"
php artisan filament:install --panels

# Instalar dependências de qualidade
composer require --dev larastan/larastan pestphp/pest rector/rector laravel/pint

# Instalar dependências de segurança
composer require spatie/laravel-permission spatie/laravel-activitylog

# Configurar estrutura modular
composer require nwidart/laravel-modules
php artisan module:make {ModuleName}

# Configurar análise estática
./vendor/bin/phpstan analyse --memory-limit=2G

# Executar testes
./vendor/bin/pest --parallel

# Formatar código
./vendor/bin/pint
```

---

## 12. STACK RECOMENDADA

| Componente | Versão Recomendada |
|------------|-------------------|
| PHP | 8.2+ |
| Laravel | 11.x |
| Filament | 3.x |
| Database | PostgreSQL ou MySQL 8.0+ |
| Redis | 7.x |
| Node.js | 20+ (para assets) |
| Container | Docker |
| Orquestração | Kubernetes ou Laravel Vapor |

---

## 13. ENTREGÁVEIS ESPERADOS

1. Código fonte seguindo todas as diretrizes
2. Suite de testes com coverage mínimo 80%
3. Pipeline CI/CD funcional
4. Documentação técnica
5. Dockerfile e docker-compose.yml
6. Scripts de deployment
7. Monitoring e alerting configurados
8. Runbook de operações

---

## 14. CHECKLIST RÁPIDO

### Antes de Iniciar

- [ ] Definir domínios de negócio
- [ ] Mapear entidades e relacionamentos
- [ ] Definir estrutura de módulos
- [ ] Configurar ambiente de desenvolvimento

### Durante o Desenvolvimento

- [ ] Seguir PSR-12
- [ ] Escrever testes junto com código
- [ ] Validar inputs
- [ ] Implementar logs e auditoria
- [ ] Documentar decisões (ADRs)

### Antes do Deploy

- [ ] Análise estática passing
- [ ] Testes passing com coverage mínimo
- [ ] Security scan
- [ ] Performance testing (se aplicável)
- [ ] Code review aprovado

### Pós-Deploy

- [ ] Monitoramento ativo
- [ ] Alertas configurados
- [ ] Documentação atualizada
- [ ] Runbook revisado

---

## REFERÊNCIAS

- [Laravel Documentation](https://laravel.com/docs)
- [Filament Documentation](https://filamentphp.com/docs)
- [PHPStan](https://phpstan.org/)
- [Pest PHP](https://pestphp.com/)
- [Spatie Packages](https://spatie.be/open-source/packages)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
