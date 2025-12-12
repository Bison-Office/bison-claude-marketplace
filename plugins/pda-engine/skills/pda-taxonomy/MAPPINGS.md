# PDA Taxonomy Mapping Reference

## Mapping Types Overview

The system has three distinct mapping chains:

```
┌──────────────────────────────────────────────────────────────────────────┐
│                    Inventory → Taxonomy Mapping                          │
│  (Maps vendor product data directly to taxonomy structure)               │
├──────────────────────────────────────────────────────────────────────────┤
│  InventoryTaxonomyMapping                                                │
│       │                                                                  │
│       └── InventoryTaxonomyCategoryMapping                               │
│               │                                                          │
│               └── InventoryTaxonomyCategoryAttributeMapping              │
└──────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│                    Taxonomy → Taxonomy Mapping                           │
│  (Maps between vendor/internal/marketplace taxonomies)                   │
├──────────────────────────────────────────────────────────────────────────┤
│  TaxonomyMapping                                                         │
│       │                                                                  │
│       └── TaxonomyCategoryMapping                                        │
│               │                                                          │
│               └── TaxonomyCategoryAttributeMapping                       │
└──────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│                    Marketplace → Taxonomy Mapping                        │
│  (Maps marketplace categories/attributes to taxonomy structure)          │
├──────────────────────────────────────────────────────────────────────────┤
│  MarketplaceTaxonomyMapping                                              │
│       │                                                                  │
│       └── MarketplaceTaxonomyCategoryMapping                             │
│               │                                                          │
│               └── MarketplaceTaxonomyCategoryAttributeMapping            │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Mapping Type 1: Inventory → Taxonomy

**Purpose**: Map raw vendor data to taxonomy structure

### InventoryTaxonomyMapping

Links a vendor to a taxonomy.

```
┌─────────────────────────────────────┐
│      InventoryTaxonomyMapping       │
├─────────────────────────────────────┤
│ VendorId: "VENDOR001"               │ ──────▶ Vendor.Id
│ TaxonomyId: 123                     │ ──────▶ Taxonomy.Id
└─────────────────────────────────────┘
```

### InventoryTaxonomyCategoryMapping

Maps a vendor category string to a taxonomy category.

```
┌─────────────────────────────────────────────────────────────┐
│           InventoryTaxonomyCategoryMapping                  │
├─────────────────────────────────────────────────────────────┤
│ InventoryTaxonomyMappingId: 1                               │
│ VendorCategory: "Electronics -> Computers" ────────────────▶│ String match to VendorCategory.Category
│ TaxonomyCategoryId: 456            ────────────────────────▶│ TaxonomyCategory.Id
│ InventoryStateHash: "abc123..."    ────────────────────────▶│ Hash of vendor attribute names
│ TaxonomyStateHash: "def456..."     ────────────────────────▶│ Hash of taxonomy attribute IDs
│ StateHash: "ghi789..."             ────────────────────────▶│ Combined hash
└─────────────────────────────────────────────────────────────┘
```

### InventoryTaxonomyCategoryAttributeMapping

Maps vendor attribute names to taxonomy attributes.

```
┌─────────────────────────────────────────────────────────────┐
│      InventoryTaxonomyCategoryAttributeMapping              │
├─────────────────────────────────────────────────────────────┤
│ InventoryTaxonomyCategoryMappingId: 10                      │
│ VendorAttribute: "Brand Name"      ────────────────────────▶│ String (attribute name from vendor)
│ TaxonomyCategoryAttributeId: 789   ────────────────────────▶│ TaxonomyCategoryAttribute.Id
└─────────────────────────────────────────────────────────────┘
```

**Key Distinction**: VendorAttribute is stored as STRING (name), not as FK. This allows mapping vendor attributes that don't exist in VendorCategoryAttribute yet.

---

## Mapping Type 2: Taxonomy → Taxonomy

**Purpose**: Map between taxonomy structures (Vendor→Internal, Internal→Marketplace)

### TaxonomyMapping

Links two taxonomies together.

```
┌─────────────────────────────────────┐
│          TaxonomyMapping            │
├─────────────────────────────────────┤
│ SourceTaxonomyId: 1     ───────────▶│ Taxonomy (Vendor)
│ DestinationTaxonomyId: 2 ──────────▶│ Taxonomy (Internal)
└─────────────────────────────────────┘
```

**Constraint**: SourceTaxonomy.TaxonomyType cannot be MarketPlace

### TaxonomyCategoryMapping

Maps categories between taxonomies.

```
┌─────────────────────────────────────────────────────────────┐
│            TaxonomyCategoryMapping                          │
├─────────────────────────────────────────────────────────────┤
│ TaxonomyMappingId: 1                                        │
│ SourceCategoryId: 100      ────────────────────────────────▶│ TaxonomyCategory.Id (source)
│ DestinationCategoryId: 200 ────────────────────────────────▶│ TaxonomyCategory.Id (destination)
│ SourceStateHash: "abc..."  ────────────────────────────────▶│ Hash of source attributes
│ DestinationStateHash: "def..." ────────────────────────────▶│ Hash of destination attributes
│ StateHash: "ghi..."        ────────────────────────────────▶│ Combined hash
└─────────────────────────────────────────────────────────────┘
```

### TaxonomyCategoryAttributeMapping

Maps individual attributes between categories.

```
┌─────────────────────────────────────────────────────────────┐
│        TaxonomyCategoryAttributeMapping                     │
├─────────────────────────────────────────────────────────────┤
│ TaxonomyCategoryMappingId: 10                               │
│ SourceAttributeId: 1000    ────────────────────────────────▶│ TaxonomyCategoryAttribute.Id
│ DestinationAttributeId: 2000 ──────────────────────────────▶│ TaxonomyCategoryAttribute.Id
└─────────────────────────────────────────────────────────────┘
```

**Key Distinction**: Both source and destination are FKs to TaxonomyCategoryAttribute.

---

## Mapping Type 3: Marketplace → Taxonomy

**Purpose**: Map marketplace category/attribute data to taxonomy structure

### MarketplaceTaxonomyMapping

Links a marketplace to a taxonomy.

```
┌─────────────────────────────────────┐
│     MarketplaceTaxonomyMapping      │
├─────────────────────────────────────┤
│ MarketplaceId: "AMAZON_US"          │ ──────▶ Marketplace.Id
│ TaxonomyId: 50                      │ ──────▶ Taxonomy (MarketPlace)
└─────────────────────────────────────┘
```

### MarketplaceTaxonomyCategoryMapping

Maps marketplace categories to taxonomy categories.

```
┌─────────────────────────────────────────────────────────────┐
│           MarketplaceTaxonomyCategoryMapping                │
├─────────────────────────────────────────────────────────────┤
│ MarketplaceTaxonomyMappingId: 1                             │
│ MarketplaceCategoryId: 100   ──────────────────────────────▶│ MarketplaceCategory.Id
│ TaxonomyCategoryId: 456      ──────────────────────────────▶│ TaxonomyCategory.Id
│ MarketplaceStateHash: "abc..." ────────────────────────────▶│ Hash of marketplace category
│ TaxonomyStateHash: "def..."  ──────────────────────────────▶│ Hash of taxonomy attributes
│ StateHash: "ghi..."          ──────────────────────────────▶│ Combined hash
└─────────────────────────────────────────────────────────────┘
```

### MarketplaceTaxonomyCategoryAttributeMapping

Maps individual marketplace attributes to taxonomy attributes.

```
┌─────────────────────────────────────────────────────────────┐
│      MarketplaceTaxonomyCategoryAttributeMapping            │
├─────────────────────────────────────────────────────────────┤
│ MarketplaceTaxonomyCategoryMappingId: 10                    │
│ MarketplaceCategoryAttributeId: 1000 ──────────────────────▶│ MarketplaceCategoryAttribute.Id
│ TaxonomyCategoryAttributeId: 2000    ──────────────────────▶│ TaxonomyCategoryAttribute.Id
└─────────────────────────────────────────────────────────────┘
```

**Key Distinction**: Both sides are FKs - marketplace attributes reference MarketplaceCategoryAttribute entities (populated via CSV import), taxonomy attributes reference TaxonomyCategoryAttribute entities.

---

## Valid Mapping Directions

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Vendor    │ ──▶ │  Internal   │ ──▶ │ MarketPlace │
│  Taxonomy   │     │  Taxonomy   │     │  Taxonomy   │
└─────────────┘     └─────────────┘     └─────────────┘
       ✓                  ✓                   ✗
   (can be             (can be            (cannot be
    source)             source)            source)
```

**Rule**: MarketPlace taxonomy can ONLY be a destination, never a source.

---

## State Hash Strategy

### Purpose

State hashes enable efficient change detection:

```sql
-- Find mappings where source structure changed
SELECT * FROM taxonomy_category_mappings
WHERE source_state_hash != (
  SELECT state_hash FROM taxonomy_categories WHERE id = source_category_id
);

-- Find mappings needing review
SELECT * FROM inventory_taxonomy_category_mappings
WHERE state_hash != MD5(inventory_state_hash || '|' || taxonomy_state_hash);
```

### Hash Calculation

**TaxonomyCategory.StateHash**:
```csharp
// Sorted attribute IDs
var attributeIds = category.Attributes.Select(a => a.Id).OrderBy(x => x);
stateHash = MD5(string.Join(",", attributeIds));
```

**InventoryTaxonomyCategoryMapping**:
```csharp
// Inventory side: sorted vendor attribute names
inventoryStateHash = MD5(string.Join(",", vendorAttributes.OrderBy(x => x)));

// Taxonomy side: sorted attribute IDs
taxonomyStateHash = MD5(string.Join(",", taxonomyAttributeIds.OrderBy(x => x)));

// Combined
stateHash = MD5(inventoryStateHash + "|" + taxonomyStateHash);
```

**TaxonomyCategoryMapping**:
```csharp
sourceStateHash = sourceCategory.StateHash;
destinationStateHash = destinationCategory.StateHash;
stateHash = MD5(sourceStateHash + "|" + destinationStateHash);
```

---

## Cascade Delete Behavior

```
Taxonomy (RESTRICT)
    └── TaxonomyCategory (CASCADE from Taxonomy? No - RESTRICT)
            └── TaxonomyCategoryAttribute (CASCADE)

TaxonomyMapping
    └── TaxonomyCategoryMapping (CASCADE)
            └── TaxonomyCategoryAttributeMapping (CASCADE)

InventoryTaxonomyMapping
    └── InventoryTaxonomyCategoryMapping (CASCADE)
            └── InventoryTaxonomyCategoryAttributeMapping (CASCADE)

MarketplaceCategory
    └── MarketplaceCategoryAttribute (CASCADE)

MarketplaceTaxonomyMapping
    └── MarketplaceTaxonomyCategoryMapping (CASCADE)
            └── MarketplaceTaxonomyCategoryAttributeMapping (CASCADE)
```

**Key behaviors**:
- Deleting a taxonomy is RESTRICTED if mappings exist
- Deleting a category cascades to its attributes and any mappings involving it
- Deleting a mapping cascades through all category and attribute mappings

---

## Common Query Patterns

### Get all mappings for a vendor

```sql
SELECT
  itm.vendor_id,
  itcm.vendor_category,
  tc.name as taxonomy_category,
  itcam.vendor_attribute,
  tca.name as taxonomy_attribute
FROM inventory_taxonomy_mappings itm
JOIN inventory_taxonomy_category_mappings itcm ON itcm.inventory_taxonomy_mapping_id = itm.id
JOIN taxonomy_categories tc ON tc.id = itcm.taxonomy_category_id
LEFT JOIN inventory_taxonomy_category_attribute_mappings itcam
  ON itcam.inventory_taxonomy_category_mapping_id = itcm.id
LEFT JOIN taxonomy_category_attributes tca ON tca.id = itcam.taxonomy_category_attribute_id
WHERE itm.vendor_id = 'VENDOR001';
```

### Get mapping chain (Vendor → Internal → Marketplace)

```sql
-- Vendor to Internal
SELECT
  src_cat.name as vendor_category,
  dest_cat.name as internal_category
FROM taxonomy_mappings tm
JOIN taxonomy_category_mappings tcm ON tcm.taxonomy_mapping_id = tm.id
JOIN taxonomy_categories src_cat ON src_cat.id = tcm.source_category_id
JOIN taxonomy_categories dest_cat ON dest_cat.id = tcm.destination_category_id
JOIN taxonomies src_tax ON src_tax.id = tm.source_taxonomy_id
WHERE src_tax.taxonomy_type = 0; -- Vendor

-- Internal to Marketplace
SELECT
  src_cat.name as internal_category,
  dest_cat.name as marketplace_category
FROM taxonomy_mappings tm
JOIN taxonomy_category_mappings tcm ON tcm.taxonomy_mapping_id = tm.id
JOIN taxonomy_categories src_cat ON src_cat.id = tcm.source_category_id
JOIN taxonomy_categories dest_cat ON dest_cat.id = tcm.destination_category_id
JOIN taxonomies dest_tax ON dest_tax.id = tm.destination_taxonomy_id
WHERE dest_tax.taxonomy_type = 2; -- MarketPlace
```

### Find stale mappings (structure changed)

```sql
SELECT
  itcm.*,
  tc.state_hash as current_taxonomy_hash
FROM inventory_taxonomy_category_mappings itcm
JOIN taxonomy_categories tc ON tc.id = itcm.taxonomy_category_id
WHERE itcm.taxonomy_state_hash != tc.state_hash;
```
