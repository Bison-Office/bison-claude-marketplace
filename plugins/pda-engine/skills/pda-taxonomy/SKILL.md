---
name: pda-taxonomy
description: Reference for PDA Engine taxonomy mapping domain. Use when working with vendor product imports, CSV imports, taxonomy structures, category/attribute mappings, state hash change detection, or understanding the vendor→internal→marketplace mapping flow. Covers data models, workflows, API endpoints, and PostgreSQL bulk insert patterns.
---

# PDA Taxonomy Mapping Domain

## Overview

The PDA Engine manages **taxonomy mapping** - a system for standardizing product attribute data from multiple vendors into a canonical structure that can then be mapped to marketplace-specific requirements.

**Core Concept**: Products from vendors have varied attribute structures. Taxonomy mapping creates a unified internal representation that can be transformed to meet any marketplace's requirements.

## Three Taxonomy Types

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Vendor Taxonomy │ ──▶ │ Internal Taxonomy│ ──▶ │Marketplace Taxon.│
│   (TaxonomyType  │     │   (TaxonomyType  │     │   (TaxonomyType  │
│     = Vendor)    │     │   = Internal)    │     │  = MarketPlace)  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
     Auto-built              Named "Bison"           e.g., Amazon
   from CSV imports           (FUTURE)
```

| Type | Value | Purpose | Status |
|------|-------|---------|--------|
| `Vendor` | 0 | Auto-generated from vendor product imports | Implemented |
| `Internal` | 1 | Canonical taxonomy (single, named "Bison") | **FUTURE** |
| `MarketPlace` | 2 | Target marketplace structure (Amazon, etc.) | Implemented |

**Note**: Currently the system supports direct Vendor → Marketplace mapping. The Internal taxonomy layer will be added in the future to provide a canonical intermediate representation.

## Entity Relationship Diagram

```
Taxonomy (TaxonomyType: Vendor|Internal|MarketPlace)
├── TaxonomyCategory (Name, StateHash)
│   └── TaxonomyCategoryAttribute (Name)
│
├── TaxonomyMapping (Source → Destination taxonomy)
│   └── TaxonomyCategoryMapping (SourceCategory → DestCategory)
│       └── TaxonomyCategoryAttributeMapping (SourceAttr → DestAttr)
│
├── InventoryTaxonomyMapping (Vendor → Taxonomy)
│   └── InventoryTaxonomyCategoryMapping (VendorCategory → TaxonomyCategory)
│       └── InventoryTaxonomyCategoryAttributeMapping (VendorAttr → TaxonomyAttr)
│
└── MarketplaceTaxonomyMapping (Marketplace → Taxonomy)
    └── MarketplaceTaxonomyCategoryMapping (MarketplaceCat → TaxonomyCat)
        └── MarketplaceTaxonomyCategoryAttributeMapping (MarketplaceAttr → TaxonomyAttr)

Vendor
├── VendorCategory (Category string, Attributes JSONB, StateHash)
├── VendorCategoryAttribute (Category, Attribute name)
├── ProductCategory (Sku, Category)
└── ProductAttribute (Sku, AttributeName, AttributeValue)

Marketplace
├── MarketplaceCategory (MarketplaceId, Category, StateHash)
└── MarketplaceCategoryAttribute (MarketplaceCategoryId, Attribute, Value)
```

## Detailed References

- [ENTITIES.md](ENTITIES.md) - Complete entity property documentation
- [WORKFLOWS.md](WORKFLOWS.md) - Import and mapping workflows
- [MAPPINGS.md](MAPPINGS.md) - Mapping types and relationships
- [ENDPOINTS.md](ENDPOINTS.md) - API endpoints and database tables reference

## Key Concepts

### Hierarchical Categories

Categories are stored as hierarchical strings using " -> " separator:
```
"Electronics -> Computers -> Laptops"
"Clothing -> Men -> Shirts -> Dress Shirts"
```

### State Hash Change Detection

Each mapping level tracks state via MD5 hashes:

| Entity | Hash Contents |
|--------|---------------|
| `TaxonomyCategory.StateHash` | MD5(sorted attribute IDs) |
| `InventoryTaxonomyCategoryMapping.StateHash` | MD5(InventoryStateHash \| TaxonomyStateHash) |
| `TaxonomyCategoryMapping.StateHash` | MD5(SourceStateHash \| DestinationStateHash) |

**Purpose**: Efficiently detect when source or destination structures change, triggering re-mapping needs.

### Bidirectional Attribute Mapping

```
Vendor Attributes          Internal Taxonomy         Marketplace Taxonomy
─────────────────          ──────────────────        ────────────────────
"Product Name"      ──▶    "Title"            ──▶    "item_name"
"Brand Name"        ──▶    "Brand"            ──▶    "brand"
"Color Options"     ──▶    "Color"            ──▶    "color_name"
```

## Validation Rules

1. **Taxonomy Mapping Direction**: MarketPlace taxonomies cannot be mapping sources (only destinations)
2. **Category Ownership**: Categories must belong to the specified taxonomy
3. **Attribute Existence**: Mapped attributes must exist in their respective categories
4. **Uniqueness**: All mappings have unique constraints preventing duplicates

## Performance Patterns

- **Bulk Inserts**: Uses PostgreSQL COPY protocol via `QuickInsertSession` (10-100x faster)
- **State Hash Indexes**: All `StateHash` columns are indexed for efficient change detection
- **Cascading Deletes**: Category deletion cascades through all attribute mappings
