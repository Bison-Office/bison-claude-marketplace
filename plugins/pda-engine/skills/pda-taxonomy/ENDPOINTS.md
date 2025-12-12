# PDA Taxonomy API Endpoints & Tables Reference

## Core Tables

### Taxonomy Structure

| Table | Purpose |
|-------|---------|
| `taxonomies` | Taxonomy definitions (type, name) |
| `taxonomy_categories` | Categories within a taxonomy |
| `taxonomy_category_attributes` | Attributes within a category |

### Mapping Tables

| Table | Maps |
|-------|------|
| `taxonomy_mappings` | Taxonomy → Taxonomy |
| `taxonomy_category_mappings` | Category → Category |
| `taxonomy_category_attribute_mappings` | Attribute → Attribute |
| `inventory_taxonomy_mappings` | Vendor → Taxonomy |
| `inventory_taxonomy_category_mappings` | VendorCategory → TaxonomyCategory |
| `inventory_taxonomy_category_attribute_mappings` | VendorAttribute → TaxonomyAttribute |
| `marketplace_taxonomy_mappings` | Marketplace → Taxonomy |
| `marketplace_taxonomy_category_mappings` | MarketplaceCategory → TaxonomyCategory |
| `marketplace_taxonomy_category_attribute_mappings` | MarketplaceAttribute → TaxonomyAttribute |

### Inventory Tables

| Table | Purpose |
|-------|---------|
| `vendors` | Vendor definitions |
| `vendor_categories` | Unique category paths per vendor (with JSONB attributes) |
| `vendor_category_attributes` | Unique attribute names per vendor category |
| `product_categories` | SKU → Category mapping |
| `product_attributes` | SKU → Attribute values |

### Marketplace Tables

| Table | Purpose |
|-------|---------|
| `marketplace_categories` | Marketplace category definitions (from CSV import) |
| `marketplace_category_attributes` | Attributes for marketplace categories (with sample values) |

---

## API Endpoints

### Taxonomy Management

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/taxonomies/internal` | Create internal taxonomy |
| POST | `/taxonomies/vendor/build` | Build vendor taxonomy from imports |
| POST | `/taxonomies/marketplace/build` | Build marketplace taxonomy from DB |
| GET | `/taxonomies` | List taxonomies (paginated) |
| GET | `/taxonomies/{id}/categories` | Get categories with attributes |

### Inventory Import

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/inventory/attributes/import` | Import vendor product CSV |
| GET | `/inventory/vendors/mappings` | Get vendor mapping status |

### Marketplace Import

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/marketplaces/attributes/import` | Import marketplace category/attribute CSV |
| GET | `/marketplaces/mappings` | Get marketplace mapping status with counts |

### Category Mappings

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/taxonomy/mappings` | Create taxonomy-to-taxonomy mapping |
| POST | `/taxonomy/category-mappings` | Create taxonomy-to-taxonomy category mapping |
| POST | `/taxonomy/inventory-category-mappings` | Create inventory-to-taxonomy category mapping |
| GET | `/taxonomy/mappings/{id}/detail` | Get mapping details with perspective |
