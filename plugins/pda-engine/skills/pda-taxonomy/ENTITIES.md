# PDA Taxonomy Entities Reference

## Core Taxonomy Entities

### Taxonomy

**Table**: `taxonomies`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/Taxonomy.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `TaxonomyType` | `TaxonomyType` | Enum: Vendor(0), Internal(1), MarketPlace(2) |
| `Name` | `string` | Max 100 chars, required |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(TaxonomyType, Name)`

---

### TaxonomyCategory

**Table**: `taxonomy_categories`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/TaxonomyCategory.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `TaxonomyId` | `long` | FK to Taxonomy (restrict delete) |
| `Name` | `string` | Max 200 chars, required |
| `StateHash` | `string` | MD5 hash of attribute IDs |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `UpdatedAt` | `DateTimeOffset` | Auto-updated |
| `Attributes` | `ICollection<TaxonomyCategoryAttribute>` | Navigation (cascade delete) |

**Indexes**:
- Unique: `(TaxonomyId, Name)`
- Index: `StateHash`

**StateHash Calculation**: `MD5(sorted attribute IDs joined)`

---

### TaxonomyCategoryAttribute

**Table**: `taxonomy_category_attributes`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/TaxonomyCategoryAttribute.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `TaxonomyCategoryId` | `long` | FK to TaxonomyCategory |
| `Name` | `string` | Max 200 chars, required |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `UpdatedAt` | `DateTimeOffset` | Auto-updated |

**Indexes**:
- Unique: `(TaxonomyCategoryId, Name)`

**Future**: Will include `Type`, `Required`, validation rules

---

## Taxonomy Mapping Entities

### TaxonomyMapping

**Table**: `taxonomy_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/TaxonomyMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `SourceTaxonomyId` | `long` | FK to Taxonomy (restrict delete) |
| `SourceTaxonomy` | `Taxonomy` | Navigation |
| `DestinationTaxonomyId` | `long` | FK to Taxonomy (restrict delete) |
| `DestinationTaxonomy` | `Taxonomy` | Navigation |

**Indexes**:
- Unique: `(SourceTaxonomyId, DestinationTaxonomyId)`

**Constraint**: Source cannot be MarketPlace type

---

### TaxonomyCategoryMapping

**Table**: `taxonomy_category_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/TaxonomyCategoryMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `TaxonomyMappingId` | `long` | FK to TaxonomyMapping (cascade delete) |
| `SourceCategoryId` | `long` | FK to TaxonomyCategory (cascade delete) |
| `DestinationCategoryId` | `long` | FK to TaxonomyCategory (cascade delete) |
| `SourceStateHash` | `string` | Hash of source attributes |
| `DestinationStateHash` | `string` | Hash of destination attributes |
| `StateHash` | `string` | Combined hash |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `TaxonomyCategoryAttributeMappings` | `ICollection<...>` | Navigation (cascade delete) |

**Indexes**:
- Unique: `(TaxonomyMappingId, SourceCategoryId, DestinationCategoryId)`
- Index: `SourceStateHash`, `DestinationStateHash`, `StateHash`

**StateHash Calculation**: `MD5(SourceStateHash + '|' + DestinationStateHash)`

---

### TaxonomyCategoryAttributeMapping

**Table**: `taxonomy_category_attribute_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/TaxonomyCategoryAttributeMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `TaxonomyCategoryMappingId` | `long` | FK to TaxonomyCategoryMapping |
| `SourceAttributeId` | `long` | FK to TaxonomyCategoryAttribute (cascade delete) |
| `DestinationAttributeId` | `long` | FK to TaxonomyCategoryAttribute (cascade delete) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(TaxonomyCategoryMappingId, SourceAttributeId, DestinationAttributeId)`

---

## Inventory-to-Taxonomy Mapping Entities

### InventoryTaxonomyMapping

**Table**: `inventory_taxonomy_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/InventoryTaxonomyMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `VendorId` | `string` | Max 20 chars, required |
| `TaxonomyId` | `long` | FK to Taxonomy (restrict delete) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(VendorId, TaxonomyId)`

---

### InventoryTaxonomyCategoryMapping

**Table**: `inventory_taxonomy_category_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/InventoryTaxonomyCategoryMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `InventoryTaxonomyMappingId` | `long` | FK (cascade delete) |
| `VendorCategory` | `string` | Max 200 chars, required |
| `TaxonomyCategoryId` | `long` | FK to TaxonomyCategory (cascade delete) |
| `InventoryStateHash` | `string` | Hash of vendor attributes |
| `TaxonomyStateHash` | `string` | Hash of taxonomy attributes |
| `StateHash` | `string` | Combined hash |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `InventoryTaxonomyCategoryAttributeMappings` | `ICollection<...>` | Navigation |

**Indexes**:
- Unique: `(InventoryTaxonomyMappingId, VendorCategory, TaxonomyCategoryId)`
- Index: `InventoryStateHash`, `TaxonomyStateHash`, `StateHash`

---

### InventoryTaxonomyCategoryAttributeMapping

**Table**: `inventory_taxonomy_category_attribute_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/InventoryTaxonomyCategoryAttributeMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `InventoryTaxonomyCategoryMappingId` | `long` | FK (cascade delete) |
| `VendorAttribute` | `string` | Max 200 chars, required |
| `TaxonomyCategoryAttributeId` | `long` | FK (cascade delete) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(InventoryTaxonomyCategoryMappingId, VendorAttribute, TaxonomyCategoryAttributeId)`

---

## Marketplace Entities

### MarketplaceCategory

**Table**: `marketplace_categories`
**File**: `src/backend/PdaEngine/Modules/Marketplaces/Models/MarketplaceCategory.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `MarketplaceId` | `string` | Max 20 chars, FK to Marketplace |
| `Category` | `string` | Max 500 chars, required |
| `StateHash` | `string` | MD5 hash computed column |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `Attributes` | `ICollection<MarketplaceCategoryAttribute>` | Navigation (cascade delete) |

**Indexes**:
- Unique: `(MarketplaceId, Category)`
- Index: `StateHash`

**StateHash Calculation**: `MD5(MarketplaceId + '|' + Category)` - computed column in PostgreSQL

---

### MarketplaceCategoryAttribute

**Table**: `marketplace_category_attributes`
**File**: `src/backend/PdaEngine/Modules/Marketplaces/Models/MarketplaceCategoryAttribute.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `MarketplaceCategoryId` | `long` | FK to MarketplaceCategory (cascade delete) |
| `Attribute` | `string` | Max 200 chars, required |
| `Value` | `string` | Max 500 chars, optional (sample/example value) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(MarketplaceCategoryId, Attribute)`

---

## Marketplace Mapping Entities

### MarketplaceTaxonomyMapping

**Table**: `marketplace_taxonomy_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/MarketplaceTaxonomyMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `MarketplaceId` | `string` | Max 20 chars, required |
| `TaxonomyId` | `long` | FK to Taxonomy (restrict delete) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(MarketplaceId, TaxonomyId)`

---

### MarketplaceTaxonomyCategoryMapping

**Table**: `marketplace_taxonomy_category_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/MarketplaceTaxonomyCategoryMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `MarketplaceTaxonomyMappingId` | `long` | FK (cascade delete) |
| `MarketplaceCategoryId` | `long` | FK to MarketplaceCategory (cascade delete) |
| `TaxonomyCategoryId` | `long` | FK to TaxonomyCategory (cascade delete) |
| `MarketplaceStateHash` | `string` | Hash of marketplace category |
| `TaxonomyStateHash` | `string` | Hash of taxonomy attributes |
| `StateHash` | `string` | Combined hash |
| `CreatedAt` | `DateTimeOffset` | Auto-set |
| `MarketplaceTaxonomyCategoryAttributeMappings` | `ICollection<...>` | Navigation |

**Indexes**:
- Unique: `(MarketplaceTaxonomyMappingId, MarketplaceCategoryId, TaxonomyCategoryId)`
- Index: `MarketplaceStateHash`, `TaxonomyStateHash`, `StateHash`

---

### MarketplaceTaxonomyCategoryAttributeMapping

**Table**: `marketplace_taxonomy_category_attribute_mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Models/MarketplaceTaxonomyCategoryAttributeMapping.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `MarketplaceTaxonomyCategoryMappingId` | `long` | FK (cascade delete) |
| `MarketplaceCategoryAttributeId` | `long` | FK to MarketplaceCategoryAttribute (cascade delete) |
| `TaxonomyCategoryAttributeId` | `long` | FK to TaxonomyCategoryAttribute (cascade delete) |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(MarketplaceTaxonomyCategoryMappingId, MarketplaceCategoryAttributeId, TaxonomyCategoryAttributeId)`

---

## Inventory Entities

### Vendor

**Table**: `vendors`
**File**: `src/backend/PdaEngine/Modules/Inventory/Models/Vendor.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `string` | Max 20 chars, primary key (value-generated-never) |
| `Name` | `string` | Max 100 chars, required, unique |

---

### VendorCategory

**Table**: `vendor_categories`
**File**: `src/backend/PdaEngine/Modules/Inventory/Models/VendorCategory.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `VendorId` | `string` | Max 20 chars, FK to Vendor |
| `Category` | `string` | Max 200 chars, required |
| `StateHash` | `string` | MD5 hash computed on import |
| `Attributes` | `List<AttributeJsonModel>` | JSONB column |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(VendorId, Category)`
- Index: `StateHash`

**AttributeJsonModel** (JSONB structure):
```json
{ "Key": "Brand", "Value": "Nike" }
```

---

### VendorCategoryAttribute

**Table**: `vendor_category_attributes`
**File**: `src/backend/PdaEngine/Modules/Inventory/Models/VendorCategoryAttribute.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `VendorId` | `string` | Max 20 chars |
| `Category` | `string` | Max 200 chars |
| `Attribute` | `string` | Max 200 chars |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(VendorId, Category, Attribute)`
- Index: `(VendorId, Attribute)`

---

### ProductCategory

**Table**: `product_categories`
**File**: `src/backend/PdaEngine/Modules/Inventory/Models/ProductCategory.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `VendorId` | `string` | Max 20 chars |
| `Sku` | `string` | Max 100 chars |
| `Category` | `string` | Max 200 chars, optional |

**Indexes**:
- Unique: `(VendorId, Sku)`
- Index: `(VendorId, Category)`

---

### ProductAttribute

**Table**: `product_attributes`
**File**: `src/backend/PdaEngine/Modules/Inventory/Models/ProductAttribute.cs`

| Property | Type | Constraints |
|----------|------|-------------|
| `Id` | `long` | Primary key |
| `VendorId` | `string` | Max 20 chars |
| `Sku` | `string` | Max 50 chars |
| `AttributeName` | `string` | Max 200 chars |
| `AttributeValue` | `string` | Text field |
| `CreatedAt` | `DateTimeOffset` | Auto-set |

**Indexes**:
- Unique: `(VendorId, Sku, AttributeName)`
