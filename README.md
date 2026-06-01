#  E-Commerce Laravel — Stack Technologique Complète

> Guide de référence pour projet d'apprentissage · Laravel 11 · PHP 8.3+ · Docker · TailwindCSS · Livewire · Bamboo
> **Version 4.0 — 2026**

---

## Table des Matières

1. [Vue d'Ensemble](#1-vue-densemble)
2. [Stack Technologique](#2-stack-technologique)
3. [Gestion des Produits & Panel Admin](#3-gestion-des-produits--panel-admin)
4. [Rôles, Permissions & Cycle de Vie](#4-rôles-permissions--cycle-de-vie)
5. [Fichiers de Configuration & Structure](#5-fichiers-de-configuration--structure)
6. [Structure de Base de Données](#6-structure-de-base-de-données)
7. [Jobs, Queues & Événements](#7-jobs-queues--événements)
8. [Endpoints API](#8-endpoints-api)
9. [Gestion du Stock](#9-gestion-du-stock)
10. [Webhooks Stripe](#10-webhooks-stripe)
11. [Sécurité & Gestion des Erreurs](#11-sécurité--gestion-des-erreurs)
12. [CI/CD — Bamboo + Bitbucket](#12-cicd--bamboo--bitbucket)
13. [Stratégie de Tests](#13-stratégie-de-tests)
14. [Performance & Optimisation](#14-performance--optimisation)
15. [SEO & Sitemap](#15-seo--sitemap)
16. [Monitoring & Logs](#16-monitoring--logs)
17. [Workflow de Développement](#17-workflow-de-développement)
18. [Checklist de Démarrage](#18-checklist-de-démarrage)
19. [Objectifs d'Apprentissage](#19-objectifs-dapprentissage)
20. [Ressources](#20-ressources)

---

## 1. Vue d'Ensemble

Ce projet simule une application e-commerce complète en environnement professionnel. Chaque décision technique vise à couvrir un maximum de concepts modernes du développement web Laravel — du code local jusqu'à la production avec CI/CD Bamboo complet.

> **Objectif :** Apprendre Laravel 11 de A à Z — développement local, CI/CD Bamboo, qualité de code SonarQube, sécurité, déploiement production.

### Architecture Générale

| Couche | Technologie | Rôle |
|--------|-------------|------|
| Backend | Laravel 11 / PHP 8.3+ | API REST + Logique métier |
| Frontend | Blade + Livewire 3 + Alpine.js | Interface réactive |
| CSS | TailwindCSS 3.x | Styles utilitaires responsive |
| Base de données | MySQL 8.0+ / PostgreSQL 15+ | Persistance des données |
| Cache / Queue | Redis | Performance + Jobs asynchrones |
| Auth | Breeze + Sanctum + Spatie | Web + API + Rôles RBAC |
| Stockage | Local + AWS S3 | Fichiers et images |
| Emails | Mailpit (dev) + Brevo (prod) | Notifications transactionnelles |
| Admin Panel | FilamentPHP 3.x | Back-office sans code |
| Conteneurs | Docker + Compose v2 | Environnement reproductible |
| Versioning | Bitbucket (Git) | Dépôt de code source |
| CI/CD | **Bamboo** | Build, test, analyse, déploiement |

---

## 2. Stack Technologique

### 2.1 Backend

| Package | Version | Usage |
|---------|---------|-------|
| Laravel | 11.x | Framework principal |
| PHP | 8.3+ | Enums, fibers, readonly classes |
| Laravel Sanctum | dernière | Auth API / SPA tokens |
| Laravel Breeze | dernière | Auth web (login, register, reset) |
| Laravel Socialite | dernière | OAuth Google / Facebook |
| Spatie Permission | 6.x | Rôles et permissions RBAC |
| Laravel Fortify | dernière | 2FA TOTP |
| Laravel Horizon | dernière | Dashboard monitoring queues Redis |
| Laravel Telescope | dernière | Debug & observabilité (dev only) |
| Intervention Image | 3.x | Resize + WebP + optimisation |
| spatie/laravel-seo | dernière | Meta tags SEO dynamiques |
| spatie/laravel-sitemap | dernière | Génération sitemap XML |

### 2.2 Frontend

| Technologie | Version | Rôle |
|-------------|---------|------|
| Blade Templates | Laravel 11 | Moteur de templates principal |
| Livewire | 3.x | Composants réactifs server-side |
| Alpine.js | 3.x | Interactivité légère client-side |
| TailwindCSS | 3.x | Framework CSS utilitaire |
| Vite | 5.x | Bundler assets + Hot Module Replacement |
| Heroicons | 2.x | Icônes SVG |
| FilamentPHP | 3.x | Panel d'administration complet |

### 2.3 CI/CD & DevOps

| Outil | Rôle |
|-------|------|
| **Bamboo** | Serveur CI/CD — orchestration builds, tests, déploiements |
| Bitbucket | Dépôt Git — code source, pull requests, revues |
| Docker | Containerisation application |
| Azure Container Registry / Docker Hub | Registre images Docker |
| SonarQube | Analyse qualité de code, quality gates |
| SSH / Laravel Envoyer | Déploiement serveurs |

> **Bamboo + Bitbucket :** combinaison native Atlassian — intégration directe entre les dépôts Bitbucket et les plans Bamboo via webhooks automatiques.

### 2.4 Paiement & Emails

| Solution | Package | Usage |
|----------|---------|-------|
| Stripe | Laravel Cashier Stripe | Recommandé — mode test obligatoire |
| PayPal | srmklive/laravel-paypal | Optionnel |
| Mollie | mollie/laravel-mollie | Europe / iDEAL |
| Mailpit | axllent/mailpit (Docker) | Capture emails en développement |
| Brevo / Mailgun | SMTP config | Production — envoi réel |

---

## 3. Gestion des Produits & Panel Admin

>  **Principe fondamental :** Tu ne modifies JAMAIS le code pour ajouter un produit, changer un prix ou modifier un stock. Tout passe par le panel d'administration.

### 3.1 FilamentPHP — Solution Recommandée

```bash
composer require filament/filament
php artisan filament:install --panels
php artisan make:filament-user

php artisan make:filament-resource Product  --generate
php artisan make:filament-resource Category --generate
php artisan make:filament-resource Order    --generate
php artisan make:filament-resource Coupon   --generate
```

Accès : `http://localhost/admin`

### 3.2 Ce que tu gères SANS toucher au code

| Action | Via le panel admin | Code modifié ? |
|--------|-------------------|----------------|
| Ajouter un produit | Formulaire Créer |  Non |
| Modifier le prix | Champ éditable |  Non |
| Modifier le stock | Champ numérique |  Non |
| Changer le statut | Toggle on/off |  Non |
| Supprimer un produit | Bouton + soft delete |  Non |
| Restaurer un produit | Onglet Corbeille |  Non |
| Uploader des images | Dropzone |  Non |
| Créer une catégorie | Formulaire |  Non |
| Gérer les coupons | CRUD coupons |  Non |
| Changer statut commande | Sélecteur |  Non |
| Gérer les utilisateurs | CRUD + rôles |  Non |
| Modérer les avis | CRUD reviews |  Non |

### 3.3 Soft Deletes en Pratique

```php
use Illuminate\Database\Eloquent\SoftDeletes;
class Product extends Model {
    use SoftDeletes;
}

$product->delete();             // Remplit deleted_at — invisible partout
$product->restore();            // Annule la suppression
$product->forceDelete();        // Suppression définitive
Product::withTrashed()->get();  // Inclure les supprimés
Product::onlyTrashed()->get();  // Corbeille
```

---

## 4. Rôles, Permissions & Cycle de Vie

### 4.1 Rôles & Permissions

| Rôle | Accès |
|------|-------|
| `super-admin` | Tout sans restriction |
| `admin` | Panel admin complet + gestion users |
| `manager` | Produits + commandes (pas users) |
| `customer` | Boutique + son compte + ses commandes |

| Catégorie | Permissions |
|-----------|-------------|
| Produits | view, create, edit, delete, restore |
| Commandes | view, edit, cancel, refund |
| Utilisateurs | view, create, edit, delete, assign-roles |
| Catalogue | manage-categories, manage-tags, manage-attributes |
| Marketing | manage-coupons, manage-reviews |
| Rapports | view-reports, export-data |
| Système | view-logs, manage-settings |

### 4.2 Cycle de Vie d'une Commande

```
PENDING → PROCESSING → SHIPPED → DELIVERED → COMPLETED
   ↓           ↓
CANCELLED   REFUNDED
```

| Statut | Notification | Stock |
|--------|-------------|-------|
| `pending` |  Email confirmation | — |
| `processing` |  Email en préparation | Décrémente |
| `shipped` |  Email + tracking | — |
| `delivered` |  Email + demande avis | — |
| `cancelled` |  Email + remboursement | Restaure |
| `refunded` |  Email confirmation | Restaure |

---

## 5. Fichiers de Configuration & Structure

### 5.1 Arborescence Complète

```
.
├── app/
│   ├── Http/Controllers/
│   │   ├── Api/V1/           # API versionnée
│   │   └── Admin/            # Controllers admin
│   ├── Http/Requests/        # Form Requests
│   ├── Models/               # Eloquent Models
│   ├── Services/             # CartService, OrderService, PaymentService
│   ├── Jobs/                 # Jobs asynchrones
│   ├── Events/ + Listeners/  # Événements applicatifs
│   ├── Notifications/        # Notifications Laravel
│   ├── Observers/            # Model Observers
│   └── Policies/             # Autorisation fine
├── bamboo-specs/             # ← CI/CD Bamboo as Code
│   └── bamboo.yml            # Plans, stages, jobs, déploiements
├── docker/
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── php.ini
│   └── supervisord.conf
├── docker-compose.yml
├── sonar-project.properties
├── phpstan.neon
├── pint.json
├── .gitignore
└── README.md
```

### 5.2 Variables `.env` Complètes

```ini
APP_NAME="Mon E-Commerce"
APP_ENV=local
APP_KEY=base64:...
APP_DEBUG=true
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=ecommerce
DB_USERNAME=root
DB_PASSWORD=secret

CACHE_STORE=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
REDIS_HOST=redis
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_FROM_ADDRESS=noreply@shop.com

STRIPE_KEY=pk_test_...
STRIPE_SECRET=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=eu-west-1
AWS_BUCKET=mon-ecommerce-bucket

SENTRY_LARAVEL_DSN=https://...

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=${APP_URL}/auth/google/callback
```

### 5.3 Services Docker Compose

| Service | Image | Port | Rôle |
|---------|-------|------|------|
| app | php:8.3-fpm custom | — | Application PHP |
| nginx | nginx:alpine | 80 | Serveur web |
| mysql | mysql:8.0 | 3306 | Base de données |
| redis | redis:alpine | 6379 | Cache + Queue |
| mailpit | axllent/mailpit | 8025 | Emails dev |
| meilisearch | getmeili/meilisearch | 7700 | Recherche (optionnel) |

---

## 6. Structure de Base de Données

### 6.1 Utilisateurs & Auth

```sql
users           id, name, email, password, avatar, phone, email_verified_at, deleted_at
roles           id, name, guard_name                          -- Spatie
permissions     id, name, guard_name                          -- Spatie
model_has_roles role_id, model_type, model_id                 -- Pivot
personal_access_tokens  tokenable, name, token, abilities, expires_at  -- Sanctum
```

### 6.2 Produits & Catalogue

```sql
categories       id, name, slug (unique), parent_id, description, image, sort_order, is_active
products         id, category_id, name, slug (unique), price, compare_price, stock,
                 sku (unique), status (index), featured, meta_title, deleted_at
product_images   id, product_id, path, alt_text, is_primary, sort_order
attributes       id, name, type (select|color|size)
attribute_values id, attribute_id, value, color_code
product_attribute product_id, attribute_value_id, stock_override, price_override
tags / post_tag  id, name, slug — product_id, tag_id
```

### 6.3 Panier & Commandes

```sql
carts            id, user_id (nullable), session_id (index), expires_at
cart_items       id, cart_id, product_id, attribute_value_id, quantity, price
orders           id, user_id, order_number (unique), status (index), subtotal, tax,
                 discount, shipping, total, currency, payment_method, payment_status,
                 shipping_address (json), billing_address (json), deleted_at
order_items      id, order_id, product_id, name, sku, quantity, price, total, options_json
order_status_history  id, order_id, status, comment, is_customer_notified, user_id
```

### 6.4 Paiement & Marketing

```sql
payments  id, order_id, transaction_id, gateway, amount, currency, status, metadata_json, paid_at
shipments id, order_id, carrier, tracking_number, tracking_url, status, shipped_at, delivered_at
reviews   id, user_id, product_id, rating (1-5), title, comment, status, verified_purchase
wishlists id, user_id, product_id  -- UNIQUE(user_id, product_id)
coupons   id, code (unique), type, value, min_purchase, max_uses, uses_count, valid_from, valid_until
```

---

## 7. Jobs, Queues & Événements

### 7.1 Événements & Listeners

| Événement | Listener | Action |
|-----------|----------|--------|
| `OrderPlaced` | `SendOrderConfirmation` | Email confirmation |
| `OrderPlaced` | `DecrementProductStock` | Décrémente stock |
| `PaymentConfirmed` | `UpdateOrderStatus` | Commande → processing |
| `PaymentConfirmed` | `SendProcessingNotification` | Email préparation |
| `OrderShipped` | `SendShippingNotification` | Email + tracking |
| `OrderCancelled` | `RestoreProductStock` | Restaure stock |
| `OrderCancelled` | `InitiateRefund` | Remboursement Stripe |
| `StockLow` | `NotifyAdmins` | Alerte email admin |

### 7.2 Tâches Planifiées

| Commande | Fréquence | Action |
|----------|-----------|--------|
| `carts:clean-expired` | Quotidien 02h00 | Supprimer paniers expirés |
| `stock:send-alerts` | Quotidien 08h00 | Alertes stock bas |
| `sitemap:generate` | Quotidien 03h00 | Régénérer sitemap XML |
| `sanctum:prune-expired` | Hebdomadaire | Nettoyer tokens expirés |
| `backup:run` | Quotidien 01h00 | Backup base de données |

---

## 8. Endpoints API

> Toutes les routes préfixées `/api/v1/` — JSON uniquement.

### Auth & Catalogue (Public)
```
POST  /api/v1/auth/register|login|logout|forgot-password|reset-password
GET   /api/v1/products          # Liste paginée + filtres
GET   /api/v1/products/{slug}   # Détail
GET   /api/v1/categories        # Arbre catégories
GET   /api/v1/search?q=         # Recherche
```

### Panier & Commandes (Auth)
```
GET|POST|PATCH|DELETE  /api/v1/cart/items/{id}
POST   /api/v1/cart/coupon
GET|POST  /api/v1/orders
POST   /api/v1/orders/{id}/cancel
```

### Admin (Auth + Rôle admin)
```
GET|POST|PUT|DELETE  /api/v1/admin/products
PATCH  /api/v1/admin/products/{id}/restore
PATCH  /api/v1/admin/products/{id}/stock
GET|PATCH  /api/v1/admin/orders — /api/v1/admin/orders/{id}/status
GET    /api/v1/admin/reports/sales
```

---

## 9. Gestion du Stock

Le stock est géré **automatiquement** via les événements — jamais manuellement dans le code lors d'une commande.

```php
// Décrémentation automatique à la confirmation paiement
class DecrementProductStock {
    public function handle(PaymentConfirmed $event): void {
        foreach ($event->order->items as $item) {
            $item->product->decrement('stock', $item->quantity);
            if ($item->product->stock <= config('shop.low_stock_threshold', 5)) {
                event(new StockLow($item->product));
            }
        }
    }
}

// Restauration automatique si commande annulée
class RestoreProductStock {
    public function handle(OrderCancelled $event): void {
        foreach ($event->order->items as $item) {
            $item->product->increment('stock', $item->quantity);
        }
    }
}
```

| Comportement | Config | Résultat |
|-------------|--------|----------|
| `hide` | out_of_stock_behavior: hide | Produit masqué si stock = 0 |
| `show_unavailable` | out_of_stock_behavior: show_unavailable | Bouton Indisponible |
| `backorder` | allow_backorder: true | Commande acceptée à stock 0 |

---

## 10. Webhooks Stripe

| Événement Stripe | Action dans l'app |
|-----------------|-------------------|
| `payment_intent.succeeded` | Commande → processing, décrémenter stock |
| `payment_intent.payment_failed` | Commande → cancelled, notifier client |
| `charge.refunded` | Commande → refunded, restaurer stock |

```bash
# Test local avec Stripe CLI
stripe listen --forward-to localhost/stripe/webhook
stripe trigger payment_intent.succeeded
stripe trigger charge.refunded
```

---

## 11. Sécurité & Gestion des Erreurs

### Protections

| Menace | Protection | Configuration |
|--------|------------|---------------|
| CSRF | Token auto | `VerifyCsrfToken` middleware |
| XSS | Blade `{{ }}` | Ne jamais utiliser `{!! !!}` sans contrôle |
| SQL Injection | PDO binding | Éviter `DB::raw()` sans binding |
| Mass Assignment | `$fillable` | Définir explicitement sur tous les models |
| Rate Limiting | Laravel Limiter | Login: 5/min, API: 60/min |
| HTTPS | Let's Encrypt | Nginx + Certbot en production |

### Exception Handler

```php
// app/Exceptions/Handler.php
$this->renderable(function (ModelNotFoundException $e, Request $r) {
    if ($r->expectsJson())
        return response()->json(['message' => 'Introuvable'], 404);
    return response()->view('errors.404', [], 404);
});
$this->renderable(function (ValidationException $e, Request $r) {
    if ($r->expectsJson())
        return response()->json(['message' => 'Invalide', 'errors' => $e->errors()], 422);
});
```

---

## 12. CI/CD — Bamboo + Bitbucket

### 12.1 Architecture Globale

```
Bitbucket (Git)  →  push/PR  →  Bamboo (CI/CD)  →  Deploy
     ↑                               ↓
  Code Review              SonarQube + Tests
```

**Rôle de chaque outil :**

| Outil | Rôle | Où |
|-------|------|----|
| **Bitbucket** | Hébergement Git, Pull Requests, revues de code | Cloud / Server |
| **Bamboo** | Build, test, analyse, packaging, déploiement | Serveur dédié / Data Center |
| **SonarQube** | Analyse qualité, quality gates | Intégré au plan Bamboo |
| **Docker Hub / ACR** | Registre images Docker buildées par Bamboo | Cloud |

### 12.2 Concepts Bamboo Clés

| Concept Bamboo | Équivalent Pipelines | Description |
|---------------|---------------------|-------------|
| **Plan** | Pipeline | Unité de CI/CD complète |
| **Stage** | Step group | Groupe de jobs (peuvent être parallèles) |
| **Job** | Step | Unité de travail sur un agent |
| **Task** | Command | Action individuelle (script, test...) |
| **Agent** | Runner | Serveur qui exécute les builds |
| **Artifact** | Artifact | Fichier passé entre stages |
| **Deployment Project** | Deploy step | Déploiement vers un environnement |
| **Bamboo Specs** | pipelines.yml | Config CI/CD as Code (YAML) |

### 12.3 Structure du Plan Bamboo

```
Plan : E-Commerce Build & Deploy
│
├── Stage 1 : Build & Test          (obligatoire — bloquant)
│   └── Job : PHP Build
│       ├── Task : Checkout Bitbucket
│       ├── Task : composer install
│       ├── Task : npm install + npm run build
│       ├── Task : php artisan test (PHPUnit/Pest)
│       └── Task : Publier artifacts (build/)
│
├── Stage 2 : Code Quality          (obligatoire — bloquant)
│   └── Job : Analysis
│       ├── Task : PHPStan (niveau 6)
│       ├── Task : Laravel Pint
│       └── Task : SonarQube Scanner
│
├── Stage 3 : Quality Gate          (obligatoire — bloquant)
│   └── Job : SonarQube Gate Check
│       └── Task : Vérifier quality gate (fail si KO)
│
├── Stage 4 : Docker Build          (sur develop + main)
│   └── Job : Build Image
│       ├── Task : docker build --tag app:${bamboo.buildNumber}
│       └── Task : docker push registry/app:${bamboo.buildNumber}
│
├── Stage 5 : Deploy Staging        (sur develop — automatique)
│   └── Job : Deploy
│       ├── Task : SSH — php artisan down
│       ├── Task : SSH — docker pull + docker compose up
│       ├── Task : SSH — php artisan migrate --force
│       ├── Task : SSH — php artisan optimize
│       └── Task : SSH — php artisan up + GET /health
│
└── Stage 6 : Deploy Production     (sur main — MANUEL)
    └── Job : Deploy Prod
        ├── Task : SSH — même séquence staging
        └── Task : Health check /health → alerte si KO
```

### 12.4 Bamboo Specs — bamboo.yml

```yaml
# bamboo-specs/bamboo.yml
---
version: 2
plan:
  project-key: ECOM
  key: BUILD
  name: E-Commerce Build & Deploy

stages:
  - Build & Test:
      jobs:
        - Build:
            tasks:
              - checkout:
                  repository: ecommerce-laravel
              - script:
                  interpreter: SHELL
                  scripts:
                    - composer install --no-interaction --prefer-dist
                    - npm ci && npm run build
                    - cp .env.bamboo .env
                    - php artisan key:generate
                    - php artisan migrate --seed
                    - php artisan test --coverage-clover=coverage.xml
            artifacts:
              - name: coverage-report
                location: coverage.xml
                copy-pattern: "**"

  - Code Quality:
      jobs:
        - Analysis:
            tasks:
              - script:
                  scripts:
                    - ./vendor/bin/phpstan analyse --level=6
                    - ./vendor/bin/pint --test
              - sonar-scanner:
                  executable: sonar-scanner
                  properties:
                    sonar.projectKey: ecommerce
                    sonar.sources: app,routes,resources
                    sonar.php.coverage.reportPaths: coverage.xml

  - Docker Build:
      manual: false
      final: false
      jobs:
        - Docker:
            tasks:
              - script:
                  scripts:
                    - docker build -t registry/ecommerce:${bamboo.buildNumber} .
                    - docker push registry/ecommerce:${bamboo.buildNumber}

  - Deploy Staging:
      triggers:
        - polling:
            period: 30
      jobs:
        - Deploy:
            tasks:
              - ssh:
                  host: staging.monshop.com
                  username: deploy
                  scripts:
                    - docker pull registry/ecommerce:${bamboo.buildNumber}
                    - docker compose up -d
                    - php artisan migrate --force
                    - php artisan optimize
                    - curl -f http://staging.monshop.com/health

  - Deploy Production:
      manual: true        # ← Déclenchement manuel obligatoire
      jobs:
        - Deploy Prod:
            tasks:
              - ssh:
                  host: prod.monshop.com
                  username: deploy
                  scripts:
                    - docker pull registry/ecommerce:${bamboo.buildNumber}
                    - docker compose up -d
                    - php artisan migrate --force
                    - php artisan optimize
                    - curl -f https://monshop.com/health

deployment-projects:
  - name: E-Commerce Deployment
    release-versioning:
      next-version-name: release-${bamboo.buildNumber}
    environments:
      - name: Staging
        triggers:
          - after-successful-build-plan
      - name: Production
        triggers: []    # Manuel uniquement
```

### 12.5 Variables Bamboo (Secrets)

```bash
# À configurer dans Bamboo → Plan → Variables (chiffrées)
bamboo.APP_KEY          # Clé Laravel
bamboo.DB_PASSWORD      # Mot de passe DB
bamboo.STRIPE_SECRET    # Clé Stripe (test staging / live prod)
bamboo.STRIPE_WEBHOOK_SECRET
bamboo.SONAR_TOKEN      # Token SonarQube
bamboo.DOCKER_USER      # Credentials registry Docker
bamboo.DOCKER_PASS
bamboo.DEPLOY_SSH_KEY   # Clé SSH déploiement
bamboo.SENTRY_DSN
```

### 12.6 Stratégie de Branches

| Branche | Plan Bamboo déclenché | Déploiement |
|---------|----------------------|-------------|
| `feature/*` | Build + Test + Quality | Aucun |
| `develop` | Plan complet | Staging (auto) |
| `release/*` | Plan complet | Staging (auto) |
| `main` | Plan complet | Production (**manuel**) |
| `hotfix/*` | Plan complet accéléré | Production (manuel urgent) |

> **Le déploiement production est toujours manuel** dans Bamboo — approbation humaine requise avant chaque mise en production.

### 12.7 Bamboo vs Bitbucket Pipelines

| Critère | Bamboo | Bitbucket Pipelines |
|---------|--------|---------------------|
| Type | Serveur CI/CD dédié | CI/CD cloud intégré |
| Hébergement | On-premise / Data Center | Cloud uniquement |
| Agents | Local, Remote, Elastic | Cloud runners |
| Contrôle | Total (réseau, sécurité) | Limité |
| Config | bamboo-specs/bamboo.yml | bitbucket-pipelines.yml |
| Intégration Jira | Native et profonde | Basique |
| Coût | Licence Bamboo | Inclus Bitbucket Cloud |
| **Idéal pour** | Entreprises, sécurité stricte | Projets cloud agiles |

### 12.8 SonarQube — Quality Gate

| Métrique | Seuil minimum |
|----------|---------------|
| Code Coverage | ≥ 70% |
| Duplicated Lines | ≤ 3% |
| Bugs critiques | 0 |
| Vulnerabilities | 0 |
| Code Smells | Note A minimum |

---

## 13. Stratégie de Tests

| Type | Framework | Couverture | Exemples |
|------|-----------|-----------|----------|
| Tests Unitaires | Pest / PHPUnit | 80%+ | Models, Services |
| Tests Feature | Laravel Testing | 70%+ | Controllers, API |
| Tests E2E | Laravel Dusk | Parcours critiques | Checkout complet |
| Analyse Statique | PHPStan niveau 6 | 0 erreur | Types, null safety |
| Code Style | Laravel Pint | 0 violation | PSR-12 |

```xml
<!-- phpunit.xml — SQLite rapide pour tests -->
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
<env name="QUEUE_CONNECTION" value="sync"/>
<env name="MAIL_MAILER" value="array"/>
```

---

## 14. Performance & Optimisation

```bash
php artisan optimize   # config + route + view + event cache
```

```ini
; php.ini Opcache
opcache.enable=1
opcache.memory_consumption=256
opcache.validate_timestamps=0
```

```php
// Eager loading — éviter N+1
Product::with(['category', 'images', 'reviews', 'tags'])->paginate(20);

// Indexes dans migrations
$table->string('slug')->unique()->index();
$table->string('status')->index();
```

---

## 15. SEO & Sitemap

```php
// Meta tags dynamiques
seo()->title($product->name)->description($product->meta_description)
     ->image($product->primaryImage?->url)
     ->canonical(route('products.show', $product->slug));

// Sitemap — généré automatiquement chaque nuit par Bamboo scheduler
Sitemap::create()->add(Product::active()->get()->map(fn($p) =>
    Url::create(route('products.show', $p->slug))->setPriority(0.8)
))->writeToFile(public_path('sitemap.xml'));
```

```
# robots.txt
User-agent: *
Disallow: /admin/
Disallow: /api/
Sitemap: https://monshop.com/sitemap.xml
```

---

## 16. Monitoring & Logs

| Outil | Usage | Environnement |
|-------|-------|---------------|
| Laravel Telescope | Debug requests, queries, jobs | Dev uniquement |
| Laravel Horizon | Dashboard queues Redis | Dev + Production |
| Sentry | Error tracking, alertes | Staging + Production |
| UptimeRobot | Ping `/health` toutes les 5 min | Production |
| Laravel Logs | Logs daily | Tous |
| Bamboo Notifications | Alertes build fail / deploy | CI/CD |

```php
// Endpoint /health — vérifié par Bamboo après chaque deploy
Route::get('/health', function () {
    return response()->json([
        'status'   => 'ok',
        'database' => DB::connection()->getPdo() ? 'ok' : 'error',
        'redis'    => Cache::store('redis')->ping() ? 'ok' : 'error',
        'storage'  => Storage::disk('local')->exists('.gitignore') ? 'ok' : 'error',
        'queue'    => Queue::size() !== null ? 'ok' : 'error',
    ]);
});
```

---

## 17. Workflow de Développement

### 17.1 Démarrage Local

```bash
git clone <repo-bitbucket> && cd <projet>
docker compose up -d
docker compose exec app composer install
docker compose exec app npm install && npm run dev
cp .env.example .env && php artisan key:generate
php artisan migrate --seed
```

| URL | Service |
|-----|---------|
| http://localhost | Application |
| http://localhost/admin | FilamentPHP |
| http://localhost:8025 | Mailpit |
| http://localhost/horizon | Queues |
| http://localhost/telescope | Debug |

### 17.2 Flux Complet avec Bamboo

```
1. git checkout -b feature/nom-fonctionnalite
2. Développement + tests locaux
3. git push origin feature/nom
         ↓
4. Bamboo déclenche : Build → Tests → PHPStan → SonarQube
         ↓
5. Pull Request Bitbucket → Code Review
         ↓
6. Merge vers develop
         ↓
7. Bamboo : Plan complet → Deploy Staging (auto)
         ↓
8. Validation QA sur staging
         ↓
9. Merge vers main
         ↓
10. Bamboo : Plan complet → Deploy Production (MANUEL)
    → php artisan migrate --force
    → php artisan optimize
    → GET /health → 200 OK 
    → Notification Bamboo : déploiement réussi
```

### 17.3 Convention de Commits

```
feat:     Nouvelle fonctionnalité
fix:      Correction de bug
refactor: Refactorisation
test:     Tests
docs:     Documentation
chore:    Maintenance
perf:     Performance
```

---

## 18. Checklist de Démarrage

### Prérequis

- [ ] Docker Desktop installé
- [ ] Composer 2.x + Node.js 20+ + npm
- [ ] Git configuré
- [ ] Compte Bitbucket + repository privé
- [ ] **Bamboo installé et configuré** (serveur ou cloud)
- [ ] **Bamboo connecté au repository Bitbucket** (webhook)
- [ ] **Agent Bamboo opérationnel** (local ou remote)
- [ ] Compte Stripe (mode test)
- [ ] Stripe CLI installé
- [ ] SonarQube opérationnel (lié à Bamboo)

### Sprint 1 — Infrastructure

- [ ] `docker-compose.yml` complet
- [ ] `Dockerfile` PHP 8.3-fpm
- [ ] `.env.example` complet
- [ ] `README.md` avec instructions
- [ ] `bamboo-specs/bamboo.yml` créé
- [ ] **Plan Bamboo importé depuis bamboo-specs/**
- [ ] **Premier build Bamboo vert **

### Sprint 2 — Auth & Rôles

- [ ] Laravel Breeze installé
- [ ] Spatie Permission + rôles admin/manager/customer
- [ ] Seeder RolesAndPermissionsSeeder
- [ ] Seeders utilisateurs démo
- [ ] 2FA via Fortify (admin)
- [ ] Tests auth + permissions
- [ ] **Build Bamboo vert après push**

### Sprint 3 — Catalogue & Admin

- [ ] FilamentPHP installé
- [ ] Ressources Filament : Product, Category, Order, Coupon
- [ ] Migrations avec soft deletes + indexes
- [ ] Upload images + Intervention Image
- [ ] Seeders 50+ produits
- [ ] Pages boutique Blade + Livewire
- [ ] SEO meta tags + sitemap

### Sprint 4 — Panier & Commandes

- [ ] CartService (invité + connecté)
- [ ] Composant Livewire panier réactif
- [ ] CheckoutRequest (validation)
- [ ] OrderService + transitions statut
- [ ] Events OrderPlaced → email + stock
- [ ] Emails via Queue

### Sprint 5 — Paiement & Finalisation

- [ ] Stripe Checkout (mode test)
- [ ] Webhooks Stripe testés localement
- [ ] Tests E2E Dusk
- [ ] **SonarQube Quality Gate ≥ 70% **
- [ ] **Deploy Staging via Bamboo **
- [ ] **Deploy Production via Bamboo (manuel) **
- [ ] Endpoint `/health` opérationnel

---

## 19. Objectifs d'Apprentissage

| Compétence | Couvert par | Niveau cible |
|-----------|-------------|-------------|
| Laravel 11 complet | Ensemble du projet | Intermédiaire-Avancé |
| Blade + Composants | Frontend | Intermédiaire |
| Livewire 3 | Panier, filtres, admin | Intermédiaire |
| TailwindCSS responsive | Toutes les pages | Intermédiaire |
| MySQL (relations, index) | Modèles et migrations | Intermédiaire |
| Docker + Compose | Environnement local | Intermédiaire |
| Git + Bitbucket (branches, PR) | Workflow quotidien | Intermédiaire |
| **Bamboo (Plans, Specs, Agents)** | **CI/CD complet** | **Débutant-Intermédiaire** |
| **Bamboo Specs YAML** | **Pipeline as Code** | **Débutant** |
| **Bamboo Deployments** | **Staging + Production** | **Débutant** |
| SonarQube (quality gates) | Analyse qualité | Débutant |
| REST API versionnée + Sanctum | API mobile/SPA | Intermédiaire |
| Auth/Authz (Breeze, Spatie RBAC) | Sécurité | Intermédiaire |
| FilamentPHP (panel admin) | Back-office | Intermédiaire |
| File uploads + Storage (S3) | Images produits | Intermédiaire |
| Queues + Events + Notifications | Emails async | Intermédiaire |
| Tests PHPUnit/Pest + Dusk | Couverture 70%+ | Intermédiaire |
| Stripe + Webhooks | Paiement | Débutant-Intermédiaire |
| Sécurité (CSRF, XSS, rate limit) | Middleware | Intermédiaire |
| SEO + Sitemap | Spatie packages | Débutant |
| Soft Deletes | Models + Filament | Intermédiaire |
| Exception Handler | Gestion erreurs | Intermédiaire |

---

## 20. Ressources

| Ressource | URL |
|-----------|-----|
| Laravel Docs | [laravel.com/docs/11.x](https://laravel.com/docs/11.x) |
| Livewire Docs | [livewire.laravel.com](https://livewire.laravel.com) |
| FilamentPHP | [filamentphp.com](https://filamentphp.com) |
| Spatie Packages | [spatie.be/docs](https://spatie.be/docs) |
| TailwindCSS | [tailwindcss.com/docs](https://tailwindcss.com/docs) |
| Stripe Docs | [stripe.com/docs](https://stripe.com/docs) |
| Stripe CLI | [stripe.com/docs/stripe-cli](https://stripe.com/docs/stripe-cli) |
| **Bamboo Docs** | [confluence.atlassian.com/bamboo](https://confluence.atlassian.com/bamboo) |
| **Bamboo Specs** | [confluence.atlassian.com/bamboo/bamboo-specs](https://confluence.atlassian.com/bamboo/bamboo-specs-resources) |
| Pest PHP | [pestphp.com](https://pestphp.com) |
| Docker Docs | [docs.docker.com](https://docs.docker.com) |
| SonarQube | [docs.sonarqube.org](https://docs.sonarqube.org) |
| Laravel Horizon | [laravel.com/docs/11.x/horizon](https://laravel.com/docs/11.x/horizon) |

---

> **Bon apprentissage !** Ce projet couvre l'intégralité du cycle DevOps professionnel avec Laravel et Bamboo CI/CD.

*E-Commerce Laravel Stack — v4.0 · 2026 · Bamboo + Bitbucket*
