# E-Commerce Laravel — Stack Technologique Complète

> Guide de référence pour projet d'apprentissage · Laravel 11 · PHP 8.3+ · Docker · TailwindCSS · Livewire
> **Version 2.0 — 2026**

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
12. [Pipeline CI/CD](#12-pipeline-cicd)
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

Ce projet simule une application e-commerce complète en environnement professionnel. Chaque décision technique vise à couvrir un maximum de concepts modernes du développement web Laravel.

> **Objectif :** Apprendre Laravel 11 de A à Z — développement local, CI/CD, qualité de code, sécurité, déploiement production.

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
| Intervention Image | 3.x | Traitement et resize d'images |
| spatie/laravel-seo | dernière | Meta tags SEO dynamiques |
| spatie/laravel-sitemap | dernière | Génération sitemap XML |

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

### 2.3 Paiement

| Solution | Package | Usage |
|----------|---------|-------|
| Stripe | Laravel Cashier Stripe | Recommandé — mode test obligatoire |
| PayPal | srmklive/laravel-paypal | Optionnel |
| Mollie | mollie/laravel-mollie | Europe / iDEAL |

> Toujours développer en mode Sandbox/Test. Ne jamais committer de clés en clair — utiliser `.env` et secrets CI/CD.

### 2.4 Emails & Notifications

| Contexte | Outil |
|----------|-------|
| Développement | Mailpit (interface web port 8025) |
| Staging | Mailtrap |
| Production | Brevo / Mailgun / SES |
| Notifications | Laravel Notifications (email, database, Slack) |

### 2.5 Recherche & Stockage

- **Recherche basique :** Eloquent `where`/`like` avec scopes
- **Recherche avancée :** Laravel Scout + Meilisearch ou Algolia
- **Stockage local (dev) :** Laravel Storage — disk `local` et `public`
- **Stockage cloud (prod) :** AWS S3 / Cloudinary / DigitalOcean Spaces

---

## 3. Gestion des Produits & Panel Admin

> **Principe fondamental :** Tu ne modifies JAMAIS le code pour ajouter un produit, changer un prix ou modifier un stock. Tout passe par une interface d'administration.

### 3.1 Les 3 Approches Possibles

####  Option 1 — FilamentPHP (recommandée)

Package qui génère automatiquement toute l'interface admin à partir de tes modèles.

```bash
# Installation
composer require filament/filament
php artisan filament:install --panels
php artisan make:filament-user   # Créer le premier admin

# Générer les ressources CRUD
php artisan make:filament-resource Product --generate
php artisan make:filament-resource Category --generate
php artisan make:filament-resource Order --generate
php artisan make:filament-resource Coupon --generate
```

Accès : `http://localhost/admin`

Ce que génère `make:filament-resource Product --generate` automatiquement :
- Formulaire **Créer un produit** (tous les champs, upload images, sélecteur catégorie)
- **Liste des produits** avec recherche, filtres, tri par colonne, pagination
- Formulaire **Modifier** (prix, stock, statut, description...)
- Bouton **Supprimer** avec confirmation (soft delete)
- **Actions en masse** (activer/désactiver plusieurs produits)

#### Option 2 — CRUD Custom avec Livewire

Pour les cas où FilamentPHP ne convient pas ou pour l'apprentissage approfondi :

```
resources/views/admin/products/
├── index.blade.php     # Liste + recherche + filtres Livewire
├── create.blade.php    # Formulaire création
└── edit.blade.php      # Formulaire modification
app/Livewire/Admin/
├── ProductTable.php    # Composant liste avec recherche temps réel
└── ProductForm.php     # Composant formulaire réactif
```

#### Option 3 — API REST + Frontend séparé

Pour une app mobile ou un frontend React/Vue :

```
POST   /api/v1/admin/products           # Créer un produit
PUT    /api/v1/admin/products/{id}      # Modifier un produit
DELETE /api/v1/admin/products/{id}      # Supprimer (soft delete)
PATCH  /api/v1/admin/products/{id}/stock # Mettre à jour le stock uniquement
PATCH  /api/v1/admin/products/{id}/price # Mettre à jour le prix uniquement
```

### 3.2 Ce que tu gères SANS toucher au code

| Action | Via le panel | Code modifié ? |
|--------|-------------|----------------|
| Ajouter un produit | Formulaire "Créer" |  Non |
| Modifier le prix | Champ éditable |  Non |
| Modifier le stock | Champ numérique |  Non |
| Changer le statut (actif/inactif) | Toggle |  Non |
| Supprimer un produit | Bouton + soft delete |  Non |
| Restaurer un produit supprimé | Onglet corbeille |  Non |
| Ajouter des images | Dropzone upload |  Non |
| Créer une catégorie | Formulaire catégories |  Non |
| Appliquer un coupon | CRUD coupons |  Non |
| Changer le statut d'une commande | Sélecteur statut |  Non |
| Gérer les utilisateurs | CRUD users + rôles |  Non |
| Voir les statistiques | Dashboard Filament |  Non |
| Exporter les commandes | Action export CSV |  Non |

### 3.3 Quand faut-il modifier le code ?

Uniquement pour des **nouvelles fonctionnalités structurelles** :

| Cas | Action requise |
|-----|---------------|
| Ajouter une colonne `weight` à `products` | Migration + champ Filament |
| Nouveau type de produit (digital) | Migration + logique métier |
| Nouvelle règle de calcul des taxes | Modification du Service |
| Nouvelle passerelle de paiement | Package + intégration |
| Nouveau type de notification | Classe Notification + listener |

### 3.4 Soft Deletes en Pratique

Les produits, commandes et utilisateurs utilisent `SoftDeletes` — ils ne sont **jamais vraiment supprimés** de la base de données.

```php
// Dans le Model
use Illuminate\Database\Eloquent\SoftDeletes;
class Product extends Model {
    use SoftDeletes;
}

// Comportement
$product->delete();          // Remplit deleted_at — invisible partout
$product->restore();         // Annule la suppression
$product->forceDelete();     // Suppression définitive
Product::withTrashed()->get(); // Inclure les supprimés
Product::onlyTrashed()->get(); // Seulement les supprimés (corbeille)
```

Dans Filament, un onglet "Corbeille" apparaît automatiquement pour restaurer les éléments.

---

## 4. Rôles, Permissions & Cycle de Vie

### 4.1 Rôles Définis

| Rôle | Accès | Créé par |
|------|-------|----------|
| `super-admin` | Tout sans restriction | Seeder initial |
| `admin` | Panel admin complet | Super-admin |
| `manager` | Produits + commandes (pas users) | Admin |
| `customer` | Boutique + son compte + ses commandes | Auto à l'inscription |

### 4.2 Permissions Détaillées

```php
// Seeder RolesAndPermissionsSeeder
$permissions = [
    // Produits
    'view-products', 'create-products', 'edit-products',
    'delete-products', 'restore-products',

    // Commandes
    'view-orders', 'edit-orders', 'cancel-orders', 'refund-orders',

    // Utilisateurs
    'view-users', 'create-users', 'edit-users', 'delete-users',
    'assign-roles',

    // Catalogue
    'manage-categories', 'manage-tags', 'manage-attributes',

    // Marketing
    'manage-coupons', 'manage-reviews',

    // Rapports
    'view-reports', 'export-data',

    // Système
    'view-logs', 'manage-settings',
];

// Attribution aux rôles
$admin->givePermissionTo($permissions);          // Tout
$manager->givePermissionTo([
    'view-products', 'edit-products',
    'view-orders', 'edit-orders', 'cancel-orders',
    'manage-categories', 'view-reports',
]);
$customer->givePermissionTo([]);                 // Aucune permission admin
```

### 4.3 Protection des Routes

```php
// Middleware sur les routes admin
Route::middleware(['auth', 'role:admin|manager'])
    ->prefix('admin')
    ->group(function () {
        Route::resource('products', ProductController::class);
        Route::resource('orders', OrderController::class);
    });

// Vérification dans les controllers
public function destroy(Product $product) {
    $this->authorize('delete-products'); // Lance 403 si non autorisé
    $product->delete();
}
```

### 4.4 Cycle de Vie d'une Commande

```
[Client passe commande]
        ↓
   PENDING (en attente de paiement)
        ↓ paiement confirmé
   PROCESSING (en cours de préparation)
        ↓ préparée par admin
   SHIPPED (expédiée — email + tracking envoyés)
        ↓ livrée
   DELIVERED (confirmée livrée)
        ↓ optionnel
   COMPLETED (avis client possible)

Chemins alternatifs :
   PENDING     → CANCELLED (timeout paiement ou client annule)
   PROCESSING  → CANCELLED (admin annule — remboursement auto)
   SHIPPED     → REFUNDED (retour produit accepté)
   DELIVERED   → REFUNDED (litige résolu en faveur client)
```

| Statut | Déclenché par | Notification client |
|--------|--------------|---------------------|
| `pending` | Création commande |  Email confirmation |
| `processing` | Paiement confirmé |  Email "en préparation" |
| `shipped` | Admin + numéro tracking |  Email + SMS tracking |
| `delivered` | Admin ou webhook transporteur |  Email + demande avis |
| `cancelled` | Client ou admin |  Email + remboursement |
| `refunded` | Admin après retour |  Email confirmation remboursement |

---

## 5. Fichiers de Configuration & Structure

### 5.1 Arborescence du Projet Laravel

```
app/
├── Console/
│   └── Commands/           # Commandes Artisan custom
│       ├── SendLowStockAlerts.php
│       └── CleanExpiredCarts.php
├── Events/
│   ├── OrderPlaced.php
│   ├── PaymentConfirmed.php
│   └── StockLow.php
├── Exceptions/
│   └── Handler.php         # Gestion centralisée des erreurs
├── Http/
│   ├── Controllers/
│   │   ├── Auth/           # Breeze controllers
│   │   ├── Admin/          # Controllers admin (si custom)
│   │   ├── Api/V1/         # Controllers API versionnés
│   │   │   ├── ProductController.php
│   │   │   ├── CartController.php
│   │   │   └── OrderController.php
│   │   ├── CartController.php
│   │   ├── CheckoutController.php
│   │   ├── OrderController.php
│   │   └── ProductController.php
│   ├── Middleware/
│   │   ├── SecurityHeaders.php   # X-Frame, CSP, etc.
│   │   └── CheckCartOwnership.php
│   └── Requests/           # Form Requests — validation
│       ├── StoreProductRequest.php
│       ├── StoreOrderRequest.php
│       └── CheckoutRequest.php
├── Jobs/
│   ├── SendOrderConfirmationEmail.php
│   ├── SendShippingNotification.php
│   ├── ProcessStripeWebhook.php
│   └── UpdateProductStock.php
├── Listeners/
│   ├── SendOrderConfirmation.php
│   ├── DecrementProductStock.php
│   └── SendLowStockAlert.php
├── Mail/
│   ├── OrderConfirmed.php
│   ├── OrderShipped.php
│   └── OrderCancelled.php
├── Models/
│   ├── User.php
│   ├── Product.php
│   ├── Category.php
│   ├── Order.php
│   ├── OrderItem.php
│   ├── Cart.php
│   ├── CartItem.php
│   ├── Payment.php
│   ├── Coupon.php
│   └── Review.php
├── Notifications/
│   ├── OrderStatusChanged.php
│   └── LowStockAlert.php
├── Observers/
│   └── OrderObserver.php   # Déclenche events sur changement statut
├── Policies/
│   ├── ProductPolicy.php
│   └── OrderPolicy.php
├── Providers/
│   ├── AppServiceProvider.php
│   └── EventServiceProvider.php
└── Services/               # Logique métier extraite
    ├── CartService.php
    ├── OrderService.php
    ├── PaymentService.php
    └── StockService.php
```

### 5.2 Variables `.env` Complètes

```ini
# Application
APP_NAME="Mon E-Commerce"
APP_ENV=local                    # local | staging | production
APP_KEY=base64:...               # php artisan key:generate
APP_DEBUG=true                   # false en production !
APP_URL=http://localhost

# Base de données
DB_CONNECTION=mysql
DB_HOST=mysql                    # Nom service Docker
DB_PORT=3306
DB_DATABASE=ecommerce
DB_USERNAME=root
DB_PASSWORD=secret

# Cache & Sessions
CACHE_STORE=redis
SESSION_DRIVER=redis
SESSION_LIFETIME=120
QUEUE_CONNECTION=redis

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=null

# Emails
MAIL_MAILER=smtp
MAIL_HOST=mailpit                # mailpit en dev
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_FROM_ADDRESS="noreply@shop.com"
MAIL_FROM_NAME="${APP_NAME}"

# Stripe
STRIPE_KEY=pk_test_...
STRIPE_SECRET=sk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# AWS S3 (si cloud storage)
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=eu-west-1
AWS_BUCKET=mon-ecommerce-bucket

# Sentry (optionnel)
SENTRY_LARAVEL_DSN=https://...

# Filament Admin
FILAMENT_FILESYSTEM_DISK=public

# Socialite (OAuth)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI="${APP_URL}/auth/google/callback"
```

### 5.3 Services Docker Compose

| Service | Image | Port | Rôle |
|---------|-------|------|------|
| app | php:8.3-fpm (custom) | — | Application PHP |
| nginx | nginx:alpine | 80 | Serveur web |
| mysql | mysql:8.0 | 3306 | Base de données |
| redis | redis:alpine | 6379 | Cache + Queue |
| mailpit | axllent/mailpit | 8025 | Emails (dev) |
| meilisearch | getmeili/meilisearch | 7700 | Recherche (optionnel) |

### 5.4 Fichiers CI/CD & Qualité

```
bitbucket-pipelines.yml     # Pipeline CI/CD complet
sonar-project.properties    # Config SonarQube
phpstan.neon                # Analyse statique niveau 6+
pint.json                   # Style de code (preset: laravel)
.gitignore                  # Exclusions Git standard Laravel
.gitattributes              # Normalisation fins de ligne
README.md                   # Documentation avec badges CI
CONTRIBUTING.md             # Guide branches et commits
LICENSE                     # MIT recommandé
```

---

## 6. Structure de Base de Données

### 6.1 Utilisateurs & Authentification

```sql
users
  id, name, email, password, avatar, phone,
  email_verified_at, remember_token,
  deleted_at (SoftDeletes), timestamps

password_reset_tokens       email, token, created_at
personal_access_tokens      tokenable, name, token, abilities, expires_at
roles                       id, name, guard_name
permissions                 id, name, guard_name
model_has_roles             role_id, model_type, model_id
model_has_permissions       permission_id, model_type, model_id
```

### 6.2 Produits & Catalogue

```sql
categories
  id, name, slug (unique), parent_id (self-join),
  description, image, sort_order, is_active

products
  id, category_id, name, slug (unique), description,
  price, compare_price, stock, sku (unique),
  status (active|inactive|draft), featured,
  meta_title, meta_description,
  deleted_at (SoftDeletes)

product_images      id, product_id, path, alt_text, is_primary, sort_order
attributes          id, name, type (select|color|size)
attribute_values    id, attribute_id, value, color_code
product_attribute   product_id, attribute_value_id, stock_override, price_override
tags                id, name, slug (unique)
product_tag         product_id, tag_id
```

### 6.3 Panier & Commandes

```sql
carts       id, user_id (nullable), session_id (index), expires_at
cart_items  id, cart_id, product_id, attribute_value_id, quantity, price

orders
  id, user_id, order_number (unique), status, subtotal, tax,
  discount, shipping, total, currency, payment_method, payment_status,
  notes, shipping_address (json), billing_address (json),
  deleted_at (SoftDeletes)

order_items          id, order_id, product_id, name, sku, quantity, price, total, options_json
order_status_history id, order_id, status, comment, is_customer_notified, user_id
```

### 6.4 Paiement, Livraison & Marketing

```sql
payments    id, order_id, transaction_id, gateway, amount, currency,
            status, method, metadata_json, paid_at

shipments   id, order_id, carrier, tracking_number, tracking_url,
            status, shipped_at, delivered_at

reviews     id, user_id, product_id, rating (1-5), title, comment,
            status (pending|approved|rejected), verified_purchase

wishlists   id, user_id, product_id  -- UNIQUE(user_id, product_id)

coupons     id, code (unique), type (percent|fixed|free_shipping),
            value, min_purchase, max_uses, uses_count,
            valid_from, valid_until, is_active
```

### 6.5 Système Laravel (natif)

```sql
notifications       notifiable_type, notifiable_id, type, data, read_at
failed_jobs         uuid, connection, queue, payload, exception, failed_at
job_batches         id, name, total_jobs, pending_jobs, failed_jobs
cache               key, value, expiration
sessions            id, user_id, ip_address, user_agent, payload, last_activity
```

---

## 7. Jobs, Queues & Événements

### 7.1 Événements & Listeners

| Événement | Listener | Action |
|-----------|----------|--------|
| `OrderPlaced` | `SendOrderConfirmation` | Email confirmation au client |
| `OrderPlaced` | `DecrementProductStock` | Décrémente le stock |
| `OrderPlaced` | `CreatePaymentRecord` | Enregistre l'intent de paiement |
| `PaymentConfirmed` | `UpdateOrderStatus` | Passe commande en `processing` |
| `PaymentConfirmed` | `SendProcessingNotification` | Email "en préparation" |
| `OrderShipped` | `SendShippingNotification` | Email + SMS avec tracking |
| `OrderCancelled` | `RestoreProductStock` | Remet le stock |
| `OrderCancelled` | `InitiateRefund` | Remboursement Stripe auto |
| `StockLow` | `NotifyAdmins` | Alerte email à l'équipe admin |
| `ReviewSubmitted` | `NotifyAdminForModeration` | Alerte modération |

### 7.2 Jobs en Queue (asynchrones)

```php
// Tous ces jobs sont traités en arrière-plan via Redis

SendOrderConfirmationEmail::dispatch($order)->onQueue('emails');
SendShippingNotification::dispatch($order, $tracking)->onQueue('emails');
ProcessStripeWebhook::dispatch($payload)->onQueue('payments');
UpdateProductStock::dispatch($product, $quantity)->onQueue('default');
SendLowStockAlert::dispatch($product)->onQueue('notifications');
GenerateSitemapXml::dispatch()->onQueue('low-priority');
ResizeProductImage::dispatch($imagePath)->onQueue('media');
```

### 7.3 Tâches Planifiées (Scheduler)

```php
// app/Console/Kernel.php
protected function schedule(Schedule $schedule): void {
    // Nettoyer les paniers expirés (tous les jours à 2h)
    $schedule->command('carts:clean-expired')->dailyAt('02:00');

    // Alertes stock bas (chaque matin à 8h)
    $schedule->command('stock:send-alerts')->dailyAt('08:00');

    // Générer le sitemap (chaque nuit à 3h)
    $schedule->command('sitemap:generate')->dailyAt('03:00');

    // Nettoyer les tokens expirés Sanctum (hebdomadaire)
    $schedule->command('sanctum:prune-expired')->weekly();

    // Backup base de données (quotidien)
    $schedule->command('backup:run')->dailyAt('01:00');
}
```

---

## 8. Endpoints API

> Toutes les routes API sont préfixées `/api/v1/` et retournent du JSON.

### 8.1 Authentification

```
POST   /api/v1/auth/register          # Inscription
POST   /api/v1/auth/login             # Connexion → token Sanctum
POST   /api/v1/auth/logout            # Déconnexion (auth requise)
POST   /api/v1/auth/refresh           # Rafraîchir le token
POST   /api/v1/auth/forgot-password   # Envoi email reset
POST   /api/v1/auth/reset-password    # Reset avec token
```

### 8.2 Catalogue (Public)

```
GET    /api/v1/products               # Liste paginée + filtres
GET    /api/v1/products/{slug}        # Détail produit
GET    /api/v1/categories             # Arbre des catégories
GET    /api/v1/categories/{slug}/products  # Produits par catégorie
GET    /api/v1/search?q=              # Recherche produits
```

### 8.3 Panier (Auth optionnel)

```
GET    /api/v1/cart                   # Voir le panier
POST   /api/v1/cart/items             # Ajouter un produit
PATCH  /api/v1/cart/items/{id}        # Modifier la quantité
DELETE /api/v1/cart/items/{id}        # Retirer un produit
POST   /api/v1/cart/coupon            # Appliquer un coupon
DELETE /api/v1/cart/coupon            # Retirer le coupon
```

### 8.4 Commandes (Auth requis)

```
GET    /api/v1/orders                 # Mes commandes
GET    /api/v1/orders/{number}        # Détail commande
POST   /api/v1/orders                 # Passer une commande (checkout)
POST   /api/v1/orders/{id}/cancel     # Annuler (si pending/processing)
```

### 8.5 Compte Client (Auth requis)

```
GET    /api/v1/profile                # Mon profil
PUT    /api/v1/profile                # Modifier mes infos
PUT    /api/v1/profile/password       # Changer mot de passe
GET    /api/v1/wishlist               # Ma wishlist
POST   /api/v1/wishlist/{productId}   # Ajouter à la wishlist
DELETE /api/v1/wishlist/{productId}   # Retirer de la wishlist
POST   /api/v1/reviews                # Soumettre un avis
```

### 8.6 Admin (Auth + Rôle admin)

```
GET    /api/v1/admin/products         # Liste avec filtres avancés
POST   /api/v1/admin/products         # Créer
PUT    /api/v1/admin/products/{id}    # Modifier
DELETE /api/v1/admin/products/{id}    # Supprimer (soft delete)
PATCH  /api/v1/admin/products/{id}/restore  # Restaurer
PATCH  /api/v1/admin/products/{id}/stock    # Modifier stock seul
GET    /api/v1/admin/orders           # Toutes les commandes
PATCH  /api/v1/admin/orders/{id}/status     # Changer statut
GET    /api/v1/admin/reports/sales    # Rapport ventes
```

---

## 9. Gestion du Stock

### 9.1 Mécanisme Automatique

Le stock est géré automatiquement via les événements — tu n'as jamais à le gérer manuellement depuis le code lors d'une commande.

```php
// Décrément automatique à la confirmation paiement
class DecrementProductStock {
    public function handle(PaymentConfirmed $event): void {
        foreach ($event->order->items as $item) {
            $item->product->decrement('stock', $item->quantity);

            // Déclencher alerte si stock bas (< seuil)
            if ($item->product->stock <= config('shop.low_stock_threshold', 5)) {
                event(new StockLow($item->product));
            }
        }
    }
}

// Restoration automatique si commande annulée
class RestoreProductStock {
    public function handle(OrderCancelled $event): void {
        foreach ($event->order->items as $item) {
            $item->product->increment('stock', $item->quantity);
        }
    }
}
```

### 9.2 Modification Manuelle via le Panel

Dans FilamentPHP, le champ stock est directement modifiable :
- Sur la fiche produit (modification complète)
- Via une action rapide inline dans la liste ("Ajuster le stock")
- Import CSV pour mise à jour en masse

### 9.3 Alertes Stock Bas

```php
// config/shop.php
'low_stock_threshold' => 5,   // Alerte si stock <= 5
'out_of_stock_behavior' => 'hide',  // hide | show_unavailable
```

Comportements configurables :
- **`hide`** : le produit disparaît de la boutique quand stock = 0
- **`show_unavailable`** : le produit reste visible avec bouton "Indisponible"
- **Backorder** : accepter les commandes même à stock 0

---

## 10. Webhooks Stripe

### 10.1 Pourquoi les Webhooks ?

Stripe notifie ton application des événements de paiement en temps réel. Sans webhook, tu ne saurais jamais si un paiement a vraiment abouti.

```
Client paye → Stripe traite → Stripe appelle ton webhook → Tu mets à jour la commande
```

### 10.2 Événements Stripe à Écouter

| Événement Stripe | Action dans l'app |
|-----------------|-------------------|
| `payment_intent.succeeded` | Commande → `processing`, décrémenter stock |
| `payment_intent.payment_failed` | Commande → `cancelled`, notifier client |
| `charge.refunded` | Commande → `refunded`, notifier client |
| `customer.subscription.created` | Activer abonnement (si applicable) |
| `invoice.payment_succeeded` | Renouvellement abonnement |

### 10.3 Implémentation Laravel Cashier

```php
// routes/web.php
Route::post('/stripe/webhook', [StripeWebhookController::class, 'handle']);

// app/Http/Controllers/StripeWebhookController.php
use Laravel\Cashier\Http\Controllers\WebhookController;

class StripeWebhookController extends WebhookController {
    public function handlePaymentIntentSucceeded(array $payload): Response {
        $paymentIntentId = $payload['data']['object']['id'];
        $order = Order::where('stripe_payment_intent', $paymentIntentId)->first();
        $order?->markAsPaid(); // Met à jour statut + déclenche événements
        return $this->successMethod();
    }
}
```

### 10.4 Test des Webhooks en Local

```bash
# Installer Stripe CLI
stripe listen --forward-to localhost/stripe/webhook

# Simuler un paiement réussi
stripe trigger payment_intent.succeeded

# Simuler un remboursement
stripe trigger charge.refunded
```

---

## 11. Sécurité & Gestion des Erreurs

### 11.1 Protections Intégrées Laravel

| Menace | Protection | Configuration |
|--------|------------|---------------|
| CSRF | Token auto-injecté | `VerifyCsrfToken` middleware |
| XSS | Blade `{{ }}` escaping | Utiliser `{!! !!}` uniquement si nécessaire |
| SQL Injection | Eloquent PDO binding | Éviter `DB::raw()` sans binding |
| Mass Assignment | `$fillable` sur models | Définir explicitement |
| Rate Limiting | Laravel Rate Limiter | Login : 5/min, API : 60/min |
| CORS | Laravel CORS config | Restreindre origins en production |

### 11.2 Bonnes Pratiques

- HTTPS via Let's Encrypt (Nginx + Certbot) en production
- Headers : `X-Frame-Options`, `X-Content-Type-Options`, CSP
- 2FA TOTP via Laravel Fortify pour les comptes admin
- Rate limiting spécifique : `/login`, `/register`, `/api/v1/checkout`
- `APP_DEBUG=false` et `APP_ENV=production` obligatoires en prod
- Validation via **Form Requests** dédiées sur toutes les requêtes

### 11.3 Gestion Centralisée des Erreurs

```php
// app/Exceptions/Handler.php
public function register(): void {
    // Erreur 404 — page produit introuvable
    $this->renderable(function (ModelNotFoundException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json(['message' => 'Ressource introuvable'], 404);
        }
        return response()->view('errors.404', [], 404);
    });

    // Erreur 403 — accès refusé
    $this->renderable(function (AuthorizationException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json(['message' => 'Accès refusé'], 403);
        }
        return response()->view('errors.403', [], 403);
    });

    // Erreur validation — retour JSON pour l'API
    $this->renderable(function (ValidationException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json([
                'message' => 'Données invalides',
                'errors'  => $e->errors(),
            ], 422);
        }
    });

    // Envoyer les erreurs critiques à Sentry
    $this->reportable(function (Throwable $e) {
        if (app()->bound('sentry')) {
            app('sentry')->captureException($e);
        }
    });
}
```

---

## 12. Pipeline CI/CD

### 12.1 Étapes du Pipeline

| Étape | Actions | Déclencheur |
|-------|---------|-------------|
| 1. Build & Test | `composer install`, `npm build`, PHPUnit/Pest | Tout push / PR |
| 2. Static Analysis | PHPStan niveau 6, Laravel Pint | Tout push / PR |
| 3. SonarQube | Qualité, coverage, duplication | Tout push / PR |
| 4. Quality Gate | Bloquer si KO (coverage < 70%) | Après SonarQube |
| 5. Docker Build | Image multi-stage, tag SHA | Merge main/develop |
| 6. Push Registry | Azure Container Registry / Docker Hub | Merge main/develop |
| 7. Deploy Staging | SSH + migrate + cache clear | Merge develop (auto) |
| 8. Deploy Prod | SSH + migrate + optimize + health check | Merge main (manuel) |

> Le déploiement production doit être **déclenché manuellement** dans Bitbucket pour éviter tout accident.

### 12.2 Stratégie de Branches

| Branche | Environnement | Pipeline |
|---------|---------------|----------|
| `feature/*` | Local | Tests + Analyse (pas de deploy) |
| `develop` | Staging | Pipeline complet + deploy auto |
| `release/*` | Staging | Tests de régression + validation QA |
| `main` | Production | Pipeline complet + deploy manuel |
| `hotfix/*` | Production | Pipeline accéléré + deploy urgent |

### 12.3 Variables CI/CD Bitbucket Secrets

```bash
APP_KEY, DB_PASSWORD, STRIPE_SECRET, STRIPE_WEBHOOK_SECRET,
SONAR_TOKEN, DOCKER_USER, DOCKER_PASS,
DEPLOY_SSH_KEY, DEPLOY_HOST, SENTRY_DSN
```

### 12.4 SonarQube — Quality Gate

| Métrique | Seuil |
|----------|-------|
| Code Coverage | ≥ 70% |
| Duplicated Lines | ≤ 3% |
| Bugs critiques | 0 |
| Vulnerabilities | 0 |
| Code Smells | Note A minimum |

---

## 13. Stratégie de Tests

### 13.1 Pyramide de Tests

| Type | Framework | Couverture | Exemples |
|------|-----------|-----------|----------|
| Tests Unitaires | Pest / PHPUnit | 80%+ | Models, Services, Helpers |
| Tests Feature | Laravel Testing | 70%+ | Controllers, API endpoints |
| Tests E2E | Laravel Dusk | Parcours critiques | Checkout, Login, Panier |
| Analyse Statique | PHPStan niveau 6 | 0 erreur | Types, null safety |
| Code Style | Laravel Pint | 0 violation | PSR-12 + preset Laravel |

### 13.2 Tests Prioritaires

- **Auth :** login, register, reset, 2FA, permissions par rôle
- **Produits :** CRUD admin, upload image, soft delete, restauration
- **Stock :** décrémentation commande, restauration annulation, alerte seuil
- **Panier :** ajout, modification quantité, suppression, coupon, calcul total
- **Commandes :** création, transitions statut, annulation, remboursement
- **Webhooks :** Stripe payment succeeded, failed, refunded
- **API :** tous les endpoints avec et sans token, validation erreurs

### 13.3 Configuration SQLite pour Tests

```xml
<!-- phpunit.xml -->
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
<env name="QUEUE_CONNECTION" value="sync"/>
<env name="MAIL_MAILER" value="array"/>
<env name="CACHE_STORE" value="array"/>
```

---

## 14. Performance & Optimisation

### 14.1 Cache Laravel

```bash
php artisan config:cache    # Staging et production
php artisan route:cache     # Staging et production
php artisan view:cache      # Staging et production
php artisan event:cache     # Staging et production
php artisan optimize        # Tout en une commande
```

```ini
; php.ini — Opcache production
opcache.enable=1
opcache.memory_consumption=256
opcache.max_accelerated_files=20000
opcache.validate_timestamps=0
```

### 14.2 Optimisations Base de Données

```php
// Eager loading — éviter N+1
Product::with(['category', 'images', 'reviews', 'tags'])->paginate(20);

// Scopes réutilisables
public function scopeActive($q) { return $q->where('status', 'active'); }
public function scopeFeatured($q) { return $q->where('featured', true); }

// Indexes dans les migrations
$table->string('slug')->unique()->index();
$table->string('status')->index();
$table->foreignId('user_id')->index()->constrained();
```

### 14.3 Assets & Images

- **Vite :** minification + tree-shaking automatiques en production
- **Intervention Image :** resize + conversion WebP avant stockage
- **Lazy loading :** `loading="lazy"` sur toutes les images produit
- **CDN :** Cloudflare en frontal pour assets statiques

---

## 15. SEO & Sitemap

### 15.1 Meta Tags Dynamiques (spatie/laravel-seo)

```php
// Dans le controller produit
seo()
    ->title($product->meta_title ?? $product->name)
    ->description($product->meta_description ?? Str::limit($product->description, 160))
    ->image($product->primaryImage?->url)
    ->canonical(route('products.show', $product->slug));
```

### 15.2 Génération Sitemap (spatie/laravel-sitemap)

```php
// app/Console/Commands/GenerateSitemap.php
Sitemap::create()
    ->add(Url::create('/'))
    ->add(Url::create('/products'))
    ->add(
        Product::active()->get()->map(fn($p) =>
            Url::create(route('products.show', $p->slug))
                ->setLastModificationDate($p->updated_at)
                ->setPriority(0.8)
        )
    )
    ->add(
        Category::all()->map(fn($c) =>
            Url::create(route('categories.show', $c->slug))
                ->setPriority(0.6)
        )
    )
    ->writeToFile(public_path('sitemap.xml'));
```

### 15.3 Fichiers SEO

```
public/
├── sitemap.xml         # Généré automatiquement (cron nuit)
├── robots.txt          # Directives crawlers
└── favicon.ico
```

```
# robots.txt
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Sitemap: https://monshop.com/sitemap.xml
```

---

## 16. Monitoring & Logs

| Outil | Usage | Environnement |
|-------|-------|---------------|
| Laravel Telescope | Debug requests, queries, jobs, mails | Développement uniquement |
| Laravel Horizon | Dashboard queues, failed jobs, throughput | Dev + Production |
| Sentry | Error tracking avec alertes | Staging + Production |
| UptimeRobot | Monitoring disponibilité ping `/health` | Production |
| Laravel Logs | Logs applicatifs (stack: daily) | Tous environnements |
| Laravel Debugbar | Profiling SQL, temps réponse, mémoire | Développement uniquement |

### Endpoint `/health`

```php
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
git clone <repo-url> && cd <projet>
docker compose up -d
docker compose exec app composer install
docker compose exec app npm install && npm run dev
cp .env.example .env
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate --seed
```

| URL | Service |
|-----|---------|
| http://localhost | Application principale |
| http://localhost/admin | Panel FilamentPHP |
| http://localhost:8025 | Mailpit (emails dev) |
| http://localhost/horizon | Horizon (queues) |
| http://localhost/telescope | Telescope (debug) |

### 17.2 Flux de Déploiement

```
git commit -m "feat: description"
git push origin feature/nom
        ↓
Pipeline CI : Build → Tests → PHPStan → SonarQube
        ↓
Pull Request → Code Review
        ↓
Merge develop → Deploy STAGING (auto)
        ↓
Validation QA
        ↓
Merge main → Deploy PROD (manuel)
  → php artisan migrate --force
  → php artisan optimize
  → GET /health → 200 OK 
```

### 17.3 Convention de Commits

```
feat:     Nouvelle fonctionnalité
fix:      Correction de bug
refactor: Refactorisation sans changement comportement
test:     Ajout ou modification de tests
docs:     Documentation uniquement
chore:    Maintenance (dépendances, config)
perf:     Amélioration performance
```

---

## 18. Checklist de Démarrage

### Prérequis

- [ ] Docker Desktop installé et fonctionnel
- [ ] Composer 2.x installé
- [ ] Node.js 20+ et npm installés
- [ ] Git configuré (`user.name` et `user.email`)
- [ ] Compte Bitbucket + repository privé créé
- [ ] Compte Stripe créé (mode test activé)
- [ ] Compte Sentry créé (optionnel)
- [ ] Stripe CLI installé (pour tests webhooks)

### Sprint 1 — Infrastructure

- [ ] `docker-compose.yml` avec PHP, Nginx, MySQL, Redis, Mailpit
- [ ] `Dockerfile` PHP 8.3-fpm + extensions
- [ ] `.env.example` complet avec toutes les variables
- [ ] `README.md` avec instructions setup
- [ ] `bitbucket-pipelines.yml` initial
- [ ] Premier pipeline 

### Sprint 2 — Authentification & Rôles

- [ ] Laravel Breeze installé
- [ ] Spatie Permission + rôles `admin`, `manager`, `customer`
- [ ] Seeder `RolesAndPermissionsSeeder` avec toutes les permissions
- [ ] Seeders utilisateurs démo (1 admin + 1 manager + 10 clients)
- [ ] 2FA via Fortify pour admin
- [ ] Tests auth + permissions

### Sprint 3 — Catalogue & Admin

- [ ] FilamentPHP installé + compte admin créé
- [ ] Ressources Filament : `Product`, `Category`, `Order`, `Coupon`
- [ ] Models avec relations, soft deletes, indexes
- [ ] Migrations avec indexes explicites
- [ ] Upload images + Intervention Image (resize + WebP)
- [ ] Seeders 50+ produits Faker réalistes
- [ ] Pages boutique : catalogue, liste, détail (Blade + Livewire)
- [ ] SEO meta tags + sitemap

### Sprint 4 — Panier & Commandes

- [ ] `CartService` — panier invité (session) + connecté (DB)
- [ ] Composant Livewire panier réactif
- [ ] Checkout avec `CheckoutRequest` (validation adresse)
- [ ] `OrderService` — création + historique statuts
- [ ] Events : `OrderPlaced` → email + décrément stock
- [ ] Emails via Queue (Mailpit en dev)
- [ ] Gestion coupons

### Sprint 5 — Paiement & Finition

- [ ] Stripe Checkout (mode test)
- [ ] Webhooks Stripe via Stripe CLI (local) + handler
- [ ] `ProcessStripeWebhook` job — mise à jour statut commande
- [ ] Tests E2E Dusk — parcours checkout complet
- [ ] SonarQube Quality Gate ≥ 70% coverage
- [ ] Déploiement staging fonctionnel et validé
- [ ] Endpoint `/health` opérationnel

---

## 19. Objectifs d'Apprentissage

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
| REST API versionnée + Sanctum | API mobile/SPA | Intermédiaire |
| Auth/Authz (Breeze, Spatie RBAC) | Sécurité accès | Intermédiaire |
| FilamentPHP (panel admin) | Gestion back-office | Intermédiaire |
| File uploads + Storage (S3) | Images produits | Intermédiaire |
| Queues + Events + Notifications | Emails asynchrones | Intermédiaire |
| Tests PHPUnit/Pest + Dusk | Couverture 70%+ | Intermédiaire |
| Stripe + Webhooks | Paiement complet | Débutant-Intermédiaire |
| Sécurité (CSRF, XSS, rate limit) | Middleware + config | Intermédiaire |
| SEO (meta tags, sitemap) | Spatie packages | Débutant |
| Soft Deletes + Restore | Models + Filament | Intermédiaire |
| Exception Handler JSON/Web | Gestion erreurs | Intermédiaire |

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
| Pest PHP | [pestphp.com](https://pestphp.com) |
| Docker Docs | [docs.docker.com](https://docs.docker.com) |
| SonarQube Docs | [docs.sonarqube.org](https://docs.sonarqube.org) |
| Bitbucket Pipelines | [support.atlassian.com](https://support.atlassian.com/bitbucket-cloud/docs/get-started-with-bitbucket-pipelines/) |
| Intervention Image | [image.intervention.io](https://image.intervention.io) |
| Laravel Horizon | [laravel.com/docs/horizon](https://laravel.com/docs/11.x/horizon) |
| Laravel Telescope | [laravel.com/docs/telescope](https://laravel.com/docs/11.x/telescope) |
| Meilisearch | [meilisearch.com/docs](https://www.meilisearch.com/docs) |

---

> **Bon apprentissage !** Ce projet couvre l'intégralité du cycle de développement web professionnel avec Laravel — du code local jusqu'à la production.

---

*E-Commerce Laravel Stack — v3.0 · 2026 · Projet d'apprentissage*
