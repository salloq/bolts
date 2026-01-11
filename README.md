# Salloq Bolts

Modular apps and extensions for the Salloq ecommerce platform. Bolts add powerful functionality to your store with one-click installation.

## Overview

Bolts are Salloq's app system—modular extensions that seamlessly integrate with your store and theme. Unlike traditional plugins that require complex setup, Bolts are designed to work out of the box with any theme through a hook-based architecture.

**Key Features:**
- One-click installation and activation
- Theme-agnostic hook system
- Automatic database migrations
- Built-in admin interfaces
- Works with any Salloq theme

## What are Bolts?

Think of Bolts as "bolt-on" functionality—powerful features that attach to your store without disrupting your existing setup. Each Bolt is a self-contained module with:

- **Frontend snippets** that render in your theme
- **Admin interface** for configuration
- **Database migrations** for data storage
- **JavaScript/CSS assets** for interactivity

## Installation

### Installing from the Bolt Store

1. Navigate to **Admin → Bolts**
2. Browse available Bolts
3. Click **Install** on the Bolt you want
4. Click **Enable** to activate

### Manual Installation

```bash
# Download the Bolt
wget https://bolts.salloq.com/downloads/reviews.zip

# Extract to the bolts directory
unzip reviews.zip -d /path/to/salloq/bolts/

# Run migrations (via admin or CLI)
php artisan bolt:migrate reviews
```

## Bolt Structure

Each Bolt follows this directory structure:

```
bolts/
└── reviews/
    ├── manifest.json        # Bolt metadata and configuration
    ├── migrations/          # Database migrations
    │   └── 001_create_reviews_table.sql
    ├── snippets/            # Liquid snippets for themes
    │   ├── product-reviews.html
    │   ├── review-form.html
    │   └── star-rating.html
    ├── assets/
    │   ├── css/
    │   │   └── reviews.css
    │   └── js/
    │       └── reviews.js
    ├── admin/               # Admin interface
    │   ├── index.php
    │   └── templates/
    │       └── settings.html
    └── README.md
```

### Required Files

| File | Purpose |
|------|---------|
| `manifest.json` | Bolt metadata, hooks, settings schema |
| `migrations/*.sql` | Database schema changes |

## Creating a Bolt

### Step 1: Create the Manifest

```json
// bolts/my-bolt/manifest.json
{
  "id": "my-bolt",
  "name": "My Custom Bolt",
  "version": "1.0.0",
  "author": "Your Name",
  "description": "A description of what this bolt does",
  "icon": "⚡",
  "category": "marketing",
  
  "hooks": {
    "product_meta": "snippets/product-badge.html",
    "body_end": "snippets/popup.html",
    "head": "snippets/styles.html"
  },
  
  "assets": {
    "css": ["assets/css/my-bolt.css"],
    "js": ["assets/js/my-bolt.js"]
  },
  
  "settings_schema": [
    {
      "type": "checkbox",
      "id": "enabled",
      "label": "Enable this feature",
      "default": true
    },
    {
      "type": "color",
      "id": "accent_color",
      "label": "Accent color",
      "default": "#007bff"
    }
  ],
  
  "migrations": [
    "migrations/001_create_table.sql"
  ]
}
```

### Step 2: Create Migrations

```sql
-- migrations/001_create_my_table.sql
CREATE TABLE IF NOT EXISTS bolt_mybolt_data (
    id INT AUTO_INCREMENT PRIMARY KEY,
    store_id INT NOT NULL,
    data JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_store (store_id)
);
```

### Step 3: Create Snippets

```liquid
<!-- snippets/product-badge.html -->
{% if bolt_settings.enabled %}
  <div class="my-bolt-badge" style="background: {{ bolt_settings.accent_color }}">
    {{ bolt_settings.badge_text | default: 'Special' }}
  </div>
{% endif %}
```

### Step 4: Create Assets

```css
/* assets/css/my-bolt.css */
.my-bolt-badge {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 4px;
  color: white;
  font-size: 12px;
  font-weight: 600;
  text-transform: uppercase;
}
```

## Hook System

Bolts inject functionality into themes through hooks. Themes define hook points, and Bolts register snippets to render at those points.

### Available Hooks

| Hook | Location | Use Case |
|------|----------|----------|
| `head` | Inside `<head>` | Styles, meta tags, scripts |
| `body_start` | After `<body>` | Banners, announcements |
| `body_end` | Before `</body>` | Scripts, modals, popups |
| `header_before` | Before header | Top bar, announcements |
| `header_after` | After header | Breadcrumbs, promotions |
| `product_meta` | After product title/price | Badges, ratings, stock info |
| `product_description_after` | After description | Reviews, specs, tabs |
| `cart_item` | Each cart line item | Warranties, options |
| `cart_footer` | Cart totals area | Discounts, notes |
| `checkout_payment` | Payment section | Extra payment options |
| `footer_before` | Before footer | Newsletter, trust badges |

### Adding Hook Points to Themes

Theme developers can add custom hook points:

```liquid
<!-- In layout/theme.html -->
<head>
  {{ content_for_header }}
  {% bolt_hook 'head' %}
</head>
<body>
  {% bolt_hook 'body_start' %}
  
  {% section 'header' %}
  {% bolt_hook 'header_after' %}
  
  {{ content_for_layout }}
  
  {% bolt_hook 'footer_before' %}
  {% section 'footer' %}
  
  {% bolt_hook 'body_end' %}
</body>

<!-- In product template -->
<div class="product-info">
  <h1>{{ product.title }}</h1>
  <p class="price">{{ product.price | money }}</p>
  {% bolt_hook 'product_meta' %}
</div>
```

## Core Bolts (Free)

These Bolts are included free with every Salloq store:

### Reviews ⭐
Customer product reviews with star ratings, photo uploads, and moderation.

**Hooks:** `product_meta`, `product_description_after`

**Features:**
- 5-star rating system
- Review photos
- Verified purchase badges
- Admin moderation queue
- Review request emails

### Wishlist ❤️
Let customers save products for later with shareable wishlists.

**Hooks:** `product_meta`, `header_icons`

**Features:**
- Add to wishlist button
- Customer wishlist page
- Shareable wishlist links
- Back-in-stock notifications

### Recently Viewed 👁️
Show customers their browsing history to encourage return visits.

**Hooks:** `product_description_after`, `footer_before`

**Features:**
- Automatic product tracking
- Configurable display count
- Cookie-based (no login required)

### Trust Badges 🛡️
Display trust signals to increase conversion rates.

**Hooks:** `product_meta`, `cart_footer`, `checkout_payment`

**Features:**
- Security badges
- Payment icons
- Satisfaction guarantee
- Custom badge uploads

### Announcements 📢
Site-wide announcement bars for promotions and updates.

**Hooks:** `body_start`, `header_before`

**Features:**
- Dismissible banners
- Countdown timers
- Scheduling
- Multiple announcements

### Discounts 🏷️
Advanced discount codes and automatic promotions.

**Hooks:** `cart_footer`, `checkout_payment`

**Features:**
- Percentage/fixed discounts
- Minimum purchase requirements
- Usage limits
- Automatic discounts
- BOGO offers

### Popups 💬
Email capture and promotional popups.

**Hooks:** `body_end`

**Features:**
- Exit intent detection
- Timed popups
- Scroll triggers
- Email integration
- A/B testing

### SEO 🔍
Search engine optimization tools.

**Hooks:** `head`

**Features:**
- Meta tag management
- Structured data (JSON-LD)
- Sitemap generation
- Robots.txt editor
- Social meta tags

## Third-Party Bolts

The Bolt Store features premium Bolts from third-party developers:

| Bolt | Developer | Price | Description |
|------|-----------|-------|-------------|
| **Subscriptions** | Salloq Pro | $29/mo | Recurring billing & subscription boxes |
| **Loyalty Points** | Salloq Pro | $19/mo | Points-based rewards program |
| **Product Bundles** | ThemeForge | $15/mo | Create and sell product bundles |
| **Pre-Orders** | AppWorks | $12/mo | Accept pre-orders for upcoming products |
| **Size Guide** | FitTech | $9/mo | Interactive size charts |
| **Inventory Alerts** | StockWatch | $7/mo | Low stock notifications |

## Admin Interface

Bolts can include admin interfaces for configuration:

```php
// admin/index.php
<?php
namespace Salloq\Bolts\Reviews\Admin;

class ReviewsAdmin {
    public function settings() {
        return [
            'title' => 'Reviews Settings',
            'template' => 'templates/settings.html',
            'data' => [
                'settings' => $this->getSettings(),
                'stats' => $this->getStats()
            ]
        ];
    }
    
    public function moderation() {
        return [
            'title' => 'Review Moderation',
            'template' => 'templates/moderation.html',
            'data' => [
                'pending' => $this->getPendingReviews()
            ]
        ];
    }
}
```

### Admin Routes

Bolts automatically get admin routes:

| Route | Description |
|-------|-------------|
| `/admin/bolts/{bolt-id}` | Bolt dashboard |
| `/admin/bolts/{bolt-id}/settings` | Bolt settings |
| `/admin/bolts/{bolt-id}/{custom}` | Custom admin pages |

## Settings Schema

Bolt settings use the same schema format as theme settings:

```json
{
  "settings_schema": [
    {
      "name": "General",
      "settings": [
        {
          "type": "checkbox",
          "id": "enabled",
          "label": "Enable reviews",
          "default": true
        },
        {
          "type": "select",
          "id": "display_style",
          "label": "Display style",
          "options": [
            { "value": "stars", "label": "Star rating" },
            { "value": "thumbs", "label": "Thumbs up/down" }
          ],
          "default": "stars"
        }
      ]
    },
    {
      "name": "Moderation",
      "settings": [
        {
          "type": "checkbox",
          "id": "auto_approve",
          "label": "Auto-approve reviews",
          "default": false
        },
        {
          "type": "number",
          "id": "min_rating",
          "label": "Minimum rating to show",
          "min": 1,
          "max": 5,
          "default": 1
        }
      ]
    }
  ]
}
```

### Setting Types

| Type | Description |
|------|-------------|
| `text` | Single line text |
| `textarea` | Multi-line text |
| `number` | Numeric input |
| `checkbox` | Boolean toggle |
| `select` | Dropdown selection |
| `color` | Color picker |
| `image_picker` | Image upload |
| `range` | Numeric slider |

## BoltManager API

The BoltManager class handles Bolt lifecycle:

```php
use Salloq\Bolts\BoltManager;

// Get the manager
$boltManager = new BoltManager($db, $storeId);

// List all Bolts
$bolts = $boltManager->getAll();

// Get enabled Bolts
$enabled = $boltManager->getEnabled();

// Enable a Bolt
$boltManager->enable('reviews');

// Disable a Bolt
$boltManager->disable('reviews');

// Get Bolt settings
$settings = $boltManager->getSettings('reviews');

// Update Bolt settings
$boltManager->updateSettings('reviews', [
    'enabled' => true,
    'auto_approve' => false
]);

// Run migrations
$boltManager->migrate('reviews');

// Get hooks for a position
$hooks = $boltManager->getHooks('product_meta');
```

## REST API

Bolts are managed through the Salloq Admin API:

### Bolt Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/admin/api/bolts` | List all Bolts |
| GET | `/admin/api/bolts/{id}` | Get Bolt details |
| POST | `/admin/api/bolts/{id}/enable` | Enable Bolt |
| POST | `/admin/api/bolts/{id}/disable` | Disable Bolt |
| GET | `/admin/api/bolts/{id}/settings` | Get Bolt settings |
| PUT | `/admin/api/bolts/{id}/settings` | Update settings |

### Example: Enable a Bolt

```bash
curl -X POST https://yourstore.salloq.com/admin/api/bolts/reviews/enable \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Example: Update Settings

```bash
curl -X PUT https://yourstore.salloq.com/admin/api/bolts/reviews/settings \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"enabled": true, "auto_approve": false}'
```

## Developing Bolts

### Development Workflow

1. **Create the Bolt structure:**
   ```bash
   mkdir -p bolts/my-bolt/{migrations,snippets,assets/{css,js},admin/templates}
   ```

2. **Create manifest.json** with your Bolt configuration

3. **Write migrations** for any database tables

4. **Create snippets** for theme integration

5. **Build the admin interface** (optional)

6. **Test locally:**
   ```bash
   # Enable via CLI
   php artisan bolt:enable my-bolt
   
   # Run migrations
   php artisan bolt:migrate my-bolt
   ```

7. **Submit to Bolt Store** (see below)

### Best Practices

- **Keep it modular:** Bolts should do one thing well
- **Theme-agnostic:** Never assume specific theme structure
- **Graceful degradation:** Work without JavaScript when possible
- **Performance:** Minimize database queries and asset size
- **Documentation:** Include a README with setup instructions

### Testing Hooks

Test your Bolt with different themes:

```php
// Test hook rendering
$boltManager = new BoltManager($db, $storeId);
$output = $boltManager->renderHook('product_meta', [
    'product' => $product,
    'settings' => $settings
]);
```

## Bolt Store Submission

To submit your Bolt to the Salloq Bolt Store:

1. **Complete the checklist:**
   - [ ] Valid manifest.json
   - [ ] All migrations tested
   - [ ] Works with core themes (Dawn, Horizon, Studio)
   - [ ] Admin interface functional
   - [ ] Documentation complete
   - [ ] Icon/thumbnail provided

2. **Package your Bolt:**
   ```bash
   zip -r my-bolt.zip bolts/my-bolt/ -x "*.DS_Store" -x "*.git*"
   ```

3. **Submit via Developer Portal:**
   Visit [developers.salloq.com/bolts](https://developers.salloq.com/bolts)

### Review Process

- **Free Bolts:** 3-5 business days
- **Paid Bolts:** 5-7 business days (includes payment setup)

## Troubleshooting

### Bolt Not Appearing

```bash
# Check if manifest.json is valid
php artisan bolt:validate my-bolt

# Check file permissions
ls -la bolts/my-bolt/
```

### Migrations Not Running

```bash
# Run migrations manually
php artisan bolt:migrate my-bolt --force

# Check migration status
php artisan bolt:migrate:status my-bolt
```

### Hooks Not Rendering

1. Verify the theme has the hook point: `{% bolt_hook 'hook_name' %}`
2. Check the Bolt is enabled: Admin → Bolts
3. Verify manifest.json hooks configuration

### Assets Not Loading

1. Check asset paths in manifest.json
2. Verify files exist in assets/ directory
3. Clear theme cache: Admin → Settings → Clear Cache

## Support

- **Documentation:** [docs.salloq.com/bolts](https://docs.salloq.com/bolts)
- **Bolt Store:** [bolts.salloq.com](https://bolts.salloq.com)
- **Developer Portal:** [developers.salloq.com](https://developers.salloq.com)
- **Community Forum:** [community.salloq.com](https://community.salloq.com)
- **GitHub Issues:** [github.com/salloq/bolts](https://github.com/salloq/bolts)

## License

Salloq Bolts is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.
