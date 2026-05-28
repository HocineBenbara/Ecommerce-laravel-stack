# E-Commerce Laravel — Stack Technologique Complète

> Guide de référence pour projet d'apprentissage · Laravel 11 · PHP 8.3+ · Docker · TailwindCSS · Livewire  
> **Version 2.0 — 2026**

---

## Table des Matières

1. [Vue d'Ensemble](#1-vue-densemble)
2. [Stack Technologique](#2-stack-technologique)
3. [Fichiers de Configuration](#3-fichiers-de-configuration)
4. [Structure de Base de Données](#4-structure-de-base-de-données)
5. [Pipeline CI/CD](#5-pipeline-cicd)
6. [Sécurité](#6-sécurité)
7. [Stratégie de Tests](#7-stratégie-de-tests)
8. [Performance & Optimisation](#8-performance--optimisation)
9. [Monitoring & Logs](#9-monitoring--logs)
10. [Workflow de Développement](#10-workflow-de-développement)
11. [Checklist de Démarrage](#11-checklist-de-démarrage)
12. [Objectifs d'Apprentissage](#12-objectifs-dapprentissage)
13. [Ressources](#13-ressources)

---

## 1. Vue d'Ensemble

Ce projet simule une application e-commerce complète en environnement professionnel. Chaque décision technique vise à couvrir un maximum de concepts modernes du développement web Laravel.

> **Objectif :** Apprendre Laravel 11 de A à Z, du développement local au déploiement en production, avec CI/CD, qualité de code et sécurité.

### Architecture Générale

| Couche | Technologie | Rôle |
|--------|-------------|------|
| Backend | Laravel 11 / PHP 8.3+ | API REST + Logique métier |
| Frontend | Blade + Livewire 3 + Alpine.js | Interface réactive |
| CSS | TailwindCSS 3.x | Styles utilitaires responsive |
| Base de données | MySQL 8.0+ / PostgreSQL 15+ | Persistance des données |
| Cache / Queue | Redis | Performance + Jobs asynchrones |
| Auth | Breeze + Sanctum + Spatie | Web + API + Rôles |
| Stockage | Local + AWS S3 | Fichiers et images |
| Emails | Mailpit (dev) + Brevo (prod) | Notifications transactionnelles |
| Conteneurs | Docker + Compose v2 | Environnement reproductible |
| CI/CD | Bitbucket Pipelines | Automatisation déploiement |

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

### 2.2 Frontend

| Technologie | Version | Rôle |
|-------------|---------|------|
| Blade Templates | Laravel 11 | Moteur de templates principal |
| Livewire | 3.x | Composants réactifs server-side |
| Alpine.js | 3.x | Interactivité légère client-side |
| TailwindCSS | 3.x | Framework CSS utilitaire |
| Vite | 5.x | Bundler assets + HMR |
| Heroicons | 2.x | Icônes SVG |
| FilamentPHP | 3.x | Panel d'administration |

### 2.3 Base de Données & ORM

| Aspect | Choix | Notes |
|--------|-------|-------|
| SGBD principal | MySQL 8.0+ | Compatible PlanetScale / Neon |
| SGBD alternatif | PostgreSQL 15+ | Recommandé pour production |
| ORM | Eloquent | Relations, scopes, casts |
| Migrations | Laravel Migrations | Versionning schéma DB |
| Seeders | Factories + Faker | Données de démo réalistes |
| Soft Deletes | SoftDeletes trait | Sur `users`, `products`, `orders` |
| Indexes | Explicites dans migrations | Sur `slug`, `email`, `order_number` |

> **Important :** Toujours ajouter des indexes sur les colonnes fréquemment filtrées (`slug`, `status`, `user_id`). Utiliser `->index()` ou `->unique()` dans les migrations.

### 2.4 Cache, Sessions & Queues

- **Cache driver :** Redis (route, config, view, application cache)
- **Sessions :** Redis ou Database selon l'environnement
- **Queue driver :** Redis avec Laravel Horizon pour monitoring
- **Scheduler :** Laravel Scheduler via cron dans Docker
- **Events/Listeners :** `order.placed`, `payment.confirmed`, `stock.low`

### 2.5 Paiement

| Solution | Package | Usage |
|----------|---------|-------|
| Stripe | Laravel Cashier Stripe | Recommandé — mode test obligatoire |
| PayPal | srmklive/laravel-paypal | Optionnel |
| Mollie | mollie/laravel-mollie | Europe / iDEAL |

>  Toujours développer en mode Sandbox/Test. Ne jamais committer de clés Stripe en clair — utiliser `.env` et les secrets CI/CD.

### 2.6 Emails & Notifications

| Contexte | Outil |
|----------|-------|
| Développement | Mailpit (interface web locale) |
| Staging | Mailtrap |
| Production | Brevo (Sendinblue) / Mailgun / SES |
| Notifications | Laravel Notifications (email, database, Slack) |

### 2.7 Recherche

- **Basique :** Eloquent `where`/`like` avec scopes
- **Avancée :** Laravel Scout + Meilisearch ou Algolia
- **Full-text :** MySQL Full-Text Index sur `name`, `description`

### 2.8 Stockage Fichiers

- **Local (dev) :** Laravel Storage — disk `local` et `public`
- **Cloud (prod) :** AWS S3 / Cloudinary / DigitalOcean Spaces
- **Traitement images :** Intervention Image (resize, WebP, optimisation)

### 2.9 Admin Panel

- **Solution recommandée :** FilamentPHP 3.x
- Alternatives : Nova (payant), Backpack, Custom Livewire

---

## 3. Fichiers de Configuration

### 3.1 Docker

```
docker/
├── Dockerfile          # PHP 8.3-fpm + extensions + Composer
├── nginx.conf          # Config Nginx pour Laravel
├── php.ini             # upload_max, memory_limit, opcache
└── supervisord.conf    # Queue worker + scheduler process
docker-compose.yml      # Orchestration services
.dockerignore           # Exclusions build (vendor/, node_modules/)
```

**Services Docker Compose :**

| Service | Image | Port |
|---------|-------|------|
| app | php:8.3-fpm (custom) | — |
| nginx | nginx:alpine | 80 |
| mysql | mysql:8.0 | 3306 |
| redis | redis:alpine | 6379 |
| mailpit | axllent/mailpit | 8025 |

### 3.2 Laravel

```
.env.example            # Template variables (committé)
.env                    # Config locale (gitignored)
.env.staging            # Config staging (secrets via CI)
.env.production         # Config production (secrets via vault)
composer.json           # Dépendances PHP
package.json            # Dépendances JS
vite.config.js          # Config Vite + HMR + Livewire plugin
tailwind.config.js      # Thème + purge CSS + plugins
phpunit.xml             # Config tests (coverage, suites)
routes/api.php          # Routes API versionnées /api/v1/
routes/web.php          # Routes web avec middleware auth
```

> **Nouveau v2 :** Versionner les routes API dès le début — préfixer avec `/api/v1/` pour éviter une migration douloureuse plus tard.

### 3.3 CI/CD & Qualité

```
bitbucket-pipelines.yml     # Pipeline CI/CD complet
sonar-project.properties    # Config SonarQube (exclusions, gate)
phpstan.neon                # Analyse statique niveau 6+
pint.json                   # Style de code (preset: laravel)
.gitignore                  # Exclusions Git standard Laravel
.gitattributes              # Normalisation fins de ligne
README.md                   # Documentation avec badges CI
CONTRIBUTING.md             # Guide branches et commits
LICENSE                     # MIT recommandé
```

---

## 4. Structure de Base de Données

### 4.1 Utilisateurs & Authentification

```sql
users
  id, name, email, password, role, avatar, phone,
  email_verified_at, remember_token,
  deleted_at (SoftDeletes), timestamps

password_reset_tokens
  email, token, created_at

personal_access_tokens          -- Sanctum
  tokenable_type, tokenable_id, name, token,
  abilities, last_used_at, expires_at, timestamps

roles                           -- Spatie Permission
  id, name, guard_name, timestamps

permissions                     -- Spatie Permission
  id, name, guard_name, timestamps

model_has_roles                 -- Pivot
  role_id, model_type, model_id

model_has_permissions           -- Pivot
  permission_id, model_type, model_id
```

### 4.2 Produits & Catalogue

```sql
categories
  id, name, slug (unique, index), parent_id (FK self),
  description, image, sort_order, is_active, timestamps

products
  id, category_id (FK), name, slug (unique, index),
  description, price, compare_price, stock, sku (unique, index),
  status (index), featured, meta_title, meta_description,
  deleted_at (SoftDeletes), timestamps

product_images
  id, product_id (FK), path, alt_text, is_primary, sort_order, timestamps

attributes
  id, name, type (select|color|size), timestamps

attribute_values
  id, attribute_id (FK), value, color_code, timestamps

product_attribute                -- Variantes produit
  product_id, attribute_value_id,
  stock_override, price_override

tags
  id, name, slug (unique), timestamps

product_tag                     -- Pivot
  product_id, tag_id
```

> **Nouveau v2 :** `compare_price` pour le prix barré, `stock_override`/`price_override` sur les variantes pour gérer les différences par taille/couleur.

### 4.3 Panier & Commandes

```sql
carts
  id, user_id (FK, nullable), session_id (index),
  expires_at, timestamps

cart_items
  id, cart_id (FK), product_id (FK), attribute_value_id (FK nullable),
  quantity, price (snapshotté), timestamps

orders
  id, user_id (FK), order_number (unique, index),
  status (index), subtotal, tax, discount, shipping, total,
  currency, payment_method, payment_status,
  notes, shipping_address (json), billing_address (json),
  deleted_at (SoftDeletes), timestamps

order_items
  id, order_id (FK), product_id (FK),
  name, sku, quantity, price, total, options_json

order_status_history
  id, order_id (FK), status, comment,
  is_customer_notified, user_id (FK), created_at
```

### 4.4 Paiement & Livraison

```sql
payments
  id, order_id (FK), transaction_id (index), gateway,
  amount, currency, status, method, metadata_json, paid_at, timestamps

shipments
  id, order_id (FK), carrier, tracking_number,
  tracking_url, status, shipped_at, delivered_at, timestamps
```

### 4.5 Interactions & Marketing

```sql
reviews
  id, user_id (FK), product_id (FK), rating (1-5),
  title, comment, status (pending|approved|rejected),
  verified_purchase, timestamps

wishlists
  id, user_id (FK), product_id (FK), timestamps
  UNIQUE(user_id, product_id)

coupons
  id, code (unique), type (percent|fixed|free_shipping),
  value, min_purchase, max_uses, uses_count,
  valid_from, valid_until, is_active, timestamps
```

### 4.6 Système (Laravel natif)

```sql
notifications       -- Laravel Notifications
failed_jobs         -- Queue failed jobs
job_batches         -- Laravel Bus batches
cache               -- Cache driver database
sessions            -- Session driver database
```

---

## 5. Pipeline CI/CD

### 5.1 Étapes du Pipeline

| Étape | Actions | Déclencheur |
|-------|---------|-------------|
| 1. Build & Test | `composer install`, `npm build`, PHPUnit/Pest | Tout push / PR |
| 2. Static Analysis | PHPStan niveau 6, Laravel Pint | Tout push / PR |
| 3. SonarQube | Qualité, coverage, duplication | Tout push / PR |
| 4. Quality Gate | Bloquer si KO (coverage < 70%) | Après SonarQube |
| 5. Docker Build | Image multi-stage, tag avec commit SHA | Merge main/develop |
| 6. Push Registry | Azure Container Registry ou Docker Hub | Merge main/develop |
| 7. Deploy Staging | SSH + migrate + cache clear | Merge develop (auto) |
| 8. Deploy Prod | SSH + migrate + cache clear + health check | Merge main (manuel) |

> **Nouveau v2 :** Le déploiement en production doit être **manuel** (trigger Bitbucket) pour éviter les accidents.

### 5.2 Stratégie de Branches

| Branche | Environnement | Pipeline |
|---------|---------------|----------|
| `feature/*` | Local uniquement | Tests + Analyse (pas de deploy) |
| `develop` | Staging | Pipeline complet + deploy staging auto |
| `release/*` | Staging | Tests de régression + validation QA |
| `main` | Production | Pipeline complet + deploy prod manuel |
| `hotfix/*` | Production | Pipeline accéléré + deploy prod urgent |

### 5.3 Variables CI/CD (Bitbucket Secrets)

```bash
APP_KEY             # Clé Laravel chiffrée
DB_PASSWORD         # Mot de passe base de données
STRIPE_SECRET       # Clé Stripe (test en staging, live en prod)
SONAR_TOKEN         # Token authentification SonarQube
DOCKER_USER         # Credentials registry
DOCKER_PASS         # Credentials registry
DEPLOY_SSH_KEY      # Clé SSH pour déploiement
SENTRY_DSN          # Tracking erreurs production
```

### 5.4 SonarQube — Quality Gate

| Métrique | Seuil Minimum |
|----------|---------------|
| Code Coverage | ≥ 70% |
| Duplicated Lines | ≤ 3% |
| Bugs critiques | 0 |
| Vulnerabilities | 0 |
| Code Smells | Note A minimum |

---

## 6. Sécurité

### 6.1 Protections Intégrées Laravel

| Menace | Protection | Configuration |
|--------|------------|---------------|
| CSRF | Token Laravel auto-injecté | `VerifyCsrfToken` middleware |
| XSS | Blade `{{ }}` escaping auto | Utiliser `{!! !!}` uniquement si nécessaire |
| SQL Injection | Eloquent PDO binding | Éviter `DB::raw()` sans binding |
| Mass Assignment | `fillable` / `guarded` sur models | Définir `$fillable` explicitement |
| Rate Limiting | Laravel Rate Limiter | Login : 5/min, API : 60/min |
| CORS | Laravel CORS config | Restreindre origins en production |

### 6.2 Bonnes Pratiques Additionnelles

- Activer HTTPS via Let's Encrypt (Nginx + Certbot) en production
- Headers de sécurité : `X-Frame-Options`, `X-Content-Type`, CSP via middleware
- 2FA TOTP avec Laravel Fortify pour tous les comptes admin
- Audit log : journaliser les actions sensibles (paiements, changements de rôles)
- Validation stricte : toutes les requêtes via **Form Requests** dédiées
- Rate limiting spécifique sur `/login`, `/register`, `/api/v1/checkout`
- `APP_DEBUG=false` et `APP_ENV=production` obligatoires en prod

---

## 7. Stratégie de Tests

### 7.1 Pyramide de Tests

| Type | Framework | Couverture cible | Exemples |
|------|-----------|-----------------|----------|
| Tests Unitaires | Pest / PHPUnit | 80%+ | Models, Services, Helpers |
| Tests Feature | Laravel Testing | 70%+ | Controllers, API endpoints |
| Tests E2E | Laravel Dusk | Parcours critiques | Checkout, Login, Panier |
| Analyse Statique | PHPStan niveau 6 | 0 erreur | Types, null safety |
| Code Style | Laravel Pint | 0 violation | PSR-12 + preset Laravel |

### 7.2 Tests Prioritaires

- **Auth :** login, register, reset password, 2FA, permissions par rôle
- **Panier :** ajout produit, modification quantité, suppression, calcul totaux, coupon
- **Commandes :** création, transitions de statut, annulation, remboursement
- **Paiement Stripe :** webhook processing, succès, échec, remboursement
- **Rôles :** accès admin vs client vs invité sur chaque route
- **API :** tous les endpoints `/api/v1/` avec et sans token

> Utiliser SQLite en mémoire (`:memory:`) pour les tests unitaires et feature — significativement plus rapide que MySQL.

```xml
<!-- phpunit.xml -->
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
```

---

## 8. Performance & Optimisation

### 8.1 Cache Laravel

```bash
php artisan config:cache    # Staging et production
php artisan route:cache     # Staging et production
php artisan view:cache      # Staging et production
php artisan event:cache     # Staging et production
php artisan optimize        # Tout en une commande
```

Opcache PHP (`php.ini`) :
```ini
opcache.enable=1
opcache.memory_consumption=256
opcache.max_accelerated_files=20000
opcache.validate_timestamps=0   ; 0 en production uniquement
```

### 8.2 Optimisations Base de Données

```php
// Eager loading systématique — éviter N+1
Product::with(['category', 'images', 'reviews', 'tags'])->paginate(20);

// Query scopes réutilisables
public function scopeActive($query) { return $query->where('status', 'active'); }
public function scopeFeatured($query) { return $query->where('featured', true); }

// Indexes dans les migrations
$table->string('slug')->unique()->index();
$table->string('status')->index();
$table->foreignId('user_id')->index()->constrained();
```

### 8.3 Assets & Images

- **Vite :** minification et tree-shaking automatiques en production
- **Images :** Intervention Image → resize + conversion WebP avant stockage
- **Lazy loading :** `loading="lazy"` sur toutes les images produit
- **CDN :** Cloudflare en frontal pour assets statiques (optionnel)

---

## 9. Monitoring & Logs

| Outil | Usage | Environnement |
|-------|-------|---------------|
| Laravel Telescope | Debug requests, queries, jobs, mails | Développement uniquement |
| Laravel Horizon | Dashboard queues Redis, failed jobs | Dev + Production |
| Sentry | Error tracking avec alertes email/Slack | Staging + Production |
| UptimeRobot | Monitoring disponibilité (ping `/health`) | Production |
| Laravel Logs | Logs applicatifs (stack: daily) | Tous environnements |
| Laravel Debugbar | Profiling SQL, temps réponse, mémoire | Développement uniquement |

### Endpoint `/health`

```php
// routes/web.php
Route::get('/health', function () {
    return response()->json([
        'status'   => 'ok',
        'database' => DB::connection()->getPdo() ? 'ok' : 'error',
        'redis'    => Cache::store('redis')->ping() ? 'ok' : 'error',
        'storage'  => Storage::disk('local')->exists('.gitignore') ? 'ok' : 'error',
    ]);
});
```

---

## 10. Workflow de Développement

### 10.1 Démarrage Local

```bash
# 1. Cloner le projet
git clone <repo-url> && cd <projet>

# 2. Démarrer les conteneurs
docker compose up -d

# 3. Installer les dépendances
docker compose exec app composer install
docker compose exec app npm install && npm run dev

# 4. Configurer l'environnement
cp .env.example .env
docker compose exec app php artisan key:generate

# 5. Base de données + données de démo
docker compose exec app php artisan migrate --seed

# 6. Accéder à l'application
open http://localhost        # Application
open http://localhost:8025   # Mailpit (emails)
open http://localhost/horizon # Horizon (queues)
```

### 10.2 Flux de Déploiement

```
git commit -m "feat: description claire"
git push origin feature/nom-fonctionnalite
        ↓
[Bitbucket] Pipeline CI déclenché
  → Build + Tests + PHPStan + SonarQube
        ↓
Pull Request → Code Review
        ↓
Merge vers develop → Deploy auto STAGING
        ↓
Validation QA sur staging
        ↓
Merge vers main → Deploy PROD (manuel)
  → php artisan migrate --force
  → php artisan optimize
  → Health check /health
```

### 10.3 Convention de Commits

```
feat: ajout panier invité
fix: correction calcul taxes TVA
refactor: extraction service paiement
test: couverture checkout complet
docs: mise à jour README
chore: upgrade dépendances
```

---

## 11. Checklist de Démarrage

### Prérequis

- [ ] Docker Desktop installé et fonctionnel
- [ ] Composer 2.x installé
- [ ] Node.js 20+ et npm installés
- [ ] Git configuré (`user.name` et `user.email`)
- [ ] Compte Bitbucket créé avec repository privé
- [ ] Compte Stripe créé (mode test activé)
- [ ] Compte Sentry créé (optionnel)
- [ ] Azure ou Render compte créé pour production (optionnel)

### Sprint 1 — Infrastructure

- [ ] `docker-compose.yml` avec PHP, Nginx, MySQL, Redis, Mailpit
- [ ] `Dockerfile` PHP 8.3-fpm + extensions requises
- [ ] `.gitignore` Laravel configuré
- [ ] `.env.example` complet avec toutes les variables
- [ ] `README.md` avec instructions setup claires
- [ ] `bitbucket-pipelines.yml` initial (build + test)
- [ ] Premier pipeline vert ✅ sur Bitbucket

### Sprint 2 — Authentification

- [ ] Laravel Breeze installé (login, register, reset password)
- [ ] Spatie Permission configuré (roles: `admin`, `customer`)
- [ ] Seeders utilisateurs de démo (1 admin + 10 clients)
- [ ] 2FA via Laravel Fortify (admin uniquement)
- [ ] Tests auth (login, register, rôles, permissions)

### Sprint 3 — Catalogue Produits

- [ ] Models `Product`, `Category`, `Tag` avec relations Eloquent
- [ ] Migrations avec soft deletes et indexes explicites
- [ ] CRUD produits via FilamentPHP (interface admin)
- [ ] Upload images avec Intervention Image (resize + WebP)
- [ ] Seeders avec 50+ produits Faker réalistes
- [ ] Pages : catalogue, liste produits, détail produit (Blade)

### Sprint 4 — Panier & Commandes

- [ ] Panier invité (session_id) + connecté (DB)
- [ ] Livewire composant panier réactif (ajout, retrait, quantité)
- [ ] Checkout avec validation adresse (Form Request)
- [ ] Gestion commandes + historique des statuts
- [ ] Emails transactionnels (confirmation, expédition) via Queue

### Sprint 5 — Paiement & Finition

- [ ] Intégration Stripe Checkout (mode test uniquement)
- [ ] Webhooks Stripe (`payment_intent.succeeded`, `charge.refunded`)
- [ ] Panel admin FilamentPHP complet (commandes, produits, clients)
- [ ] Tests E2E avec Laravel Dusk (parcours checkout complet)
- [ ] SonarQube Quality Gate passé (coverage ≥ 70%)
- [ ] Déploiement staging fonctionnel et validé

---

## 12. Objectifs d'Apprentissage

| Compétence | Couvert par | Niveau cible |
|-----------|-------------|-------------|
| Laravel 11 (routing, middleware, Eloquent) | Ensemble du projet | Intermédiaire-Avancé |
| Blade + Composants anonymes | Frontend pages | Intermédiaire |
| Livewire 3 (composants réactifs) | Panier, filtres, admin | Intermédiaire |
| TailwindCSS responsive | Toutes les pages | Intermédiaire |
| MySQL (relations, index, requêtes) | Modèles et migrations | Intermédiaire |
| Docker + Docker Compose | Environnement local | Intermédiaire |
| Git (branches, rebase, hooks) | Workflow quotidien | Intermédiaire |
| Bitbucket Pipelines (YAML, stages) | CI/CD complet | Débutant-Intermédiaire |
| SonarQube (quality gates) | Analyse qualité | Débutant |
| REST API + Sanctum | API mobile/SPA | Intermédiaire |
| Auth/Authz (Breeze, Spatie) | Sécurité accès | Intermédiaire |
| File uploads + Storage | Images produits | Intermédiaire |
| Queues + Events + Notifications | Emails asynchrones | Intermédiaire |
| Tests PHPUnit/Pest + Dusk | Couverture 70%+ | Intermédiaire |
| Stripe + Webhooks | Paiement complet | Débutant-Intermédiaire |
| Sécurité (CSRF, XSS, rate limit) | Middleware + config | Intermédiaire |
| Performance (cache, eager load) | Optimisation requêtes | Intermédiaire |
| Docker CI/CD + Registry | Build + deploy | Débutant-Intermédiaire |

---

## 13. Ressources

| Ressource | URL |
|-----------|-----|
| Laravel Docs | [laravel.com/docs/11.x](https://laravel.com/docs/11.x) |
| Livewire Docs | [livewire.laravel.com](https://livewire.laravel.com) |
| FilamentPHP | [filamentphp.com](https://filamentphp.com) |
| Spatie Packages | [spatie.be/docs](https://spatie.be/docs) |
| TailwindCSS | [tailwindcss.com/docs](https://tailwindcss.com/docs) |
| Stripe Docs | [stripe.com/docs](https://stripe.com/docs) |
| Pest PHP | [pestphp.com](https://pestphp.com) |
| Docker Docs | [docs.docker.com](https://docs.docker.com) |
| SonarQube Docs | [docs.sonarqube.org](https://docs.sonarqube.org) |
| Bitbucket Pipelines | [support.atlassian.com](https://support.atlassian.com/bitbucket-cloud/docs/get-started-with-bitbucket-pipelines/) |
| Intervention Image | [image.intervention.io](https://image.intervention.io) |
| Laravel Horizon | [laravel.com/docs/horizon](https://laravel.com/docs/11.x/horizon) |

---

>  **Bon apprentissage !** Ce projet te donnera une base solide pour développer des applications Laravel professionnelles, du code local jusqu'au déploiement en production avec CI/CD complet.

---

*E-Commerce Laravel Stack — v1.0 · 2026 · Projet d'apprentissage*
