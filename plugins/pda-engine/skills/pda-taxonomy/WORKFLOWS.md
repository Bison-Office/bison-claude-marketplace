# PDA Taxonomy Workflows

## Workflow 1: Import Vendor Products & Build Vendor Taxonomy

This is the primary entry point for getting vendor data into the system.

### Step 1: Import Vendor Product Attributes

**Endpoint**: `POST /inventory/attributes/import`
**File**: `src/backend/PdaEngine/Modules/Inventory/Endpoints/Write/ImportAttributesEndpoint.cs`

```
CSV Input Format:
┌─────────┬────────────┬────────────┬─────────┬───────┬────────┐
│ Sku     │ Category1  │ Category2  │ Brand   │ Color │ Size   │
├─────────┼────────────┼────────────┼─────────┼───────┼────────┤
│ SKU001  │ Electronics│ Computers  │ Dell    │ Black │ 15"    │
│ SKU002  │ Electronics│ Phones     │ Apple   │ White │ Large  │
└─────────┴────────────┴────────────┴─────────┴───────┴────────┘
         └──── Category Columns ────┘└──── Attribute Columns ──┘
```

**Processing Flow**:

```
1. CSV uploaded with vendor info
                ↓
2. ProductRawAttributeMapper identifies columns:
   - Category columns (concatenated with " -> ")
   - Attribute columns (everything else)
                ↓
3. Creates/updates:
   - ProductCategory: (Sku, "Electronics -> Computers")
   - ProductAttribute: (Sku, "Brand", "Dell"), (Sku, "Color", "Black"), ...
   - VendorCategory: ("Electronics -> Computers", StateHash, JSONB attributes)
   - VendorCategoryAttribute: (VendorId, Category, "Brand"), ...
```

**Key Service**: `ProductRawAttributeMapper`
- Uses ` -> ` separator for category hierarchy
- First attribute value encountered is stored in VendorCategory JSONB

---

### Step 2: Build Vendor Taxonomy

**Endpoint**: `POST /taxonomies/vendor/build`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Endpoints/Write/BuildVendorTaxonomyEndpoint.cs`

**Processing Flow**:

```
1. Get/create InventoryTaxonomyMapping for vendor
                ↓
2. Get distinct VendorCategoryAttribute.Category values
                ↓
3. For each vendor category:
   - Create TaxonomyCategory (same name)
   - Get distinct attributes for this category
   - Create TaxonomyCategoryAttribute for each
                ↓
4. Create InventoryTaxonomyCategoryMapping:
   - Links VendorCategory → TaxonomyCategory
   - Initialize state hashes (empty at first)
                ↓
5. Create InventoryTaxonomyCategoryAttributeMapping:
   - Links vendor attribute names → TaxonomyCategoryAttribute
                ↓
6. Update all state hashes via TaxonomyHashService
```

**Result**: A complete vendor taxonomy mirroring the imported data structure.

---

## Workflow 2: Create Internal (Canonical) Taxonomy

> **⚠️ FUTURE FEATURE**: The Internal Taxonomy workflow is not yet implemented. It will be added in a future release to provide a canonical intermediate layer between Vendor and Marketplace taxonomies.

The internal taxonomy will be the central hub - all vendor taxonomies will map TO it, and it will map TO marketplace taxonomies.

### Planned Features

- Only ONE internal taxonomy allowed
- Named "Bison" by default
- TaxonomyType = Internal (1)
- Categories and attributes added manually to define the canonical structure

**Current State**: The system currently supports direct Vendor → Marketplace mapping without the intermediate Internal taxonomy layer.

---

## Workflow 3: Import Marketplace Attributes & Build Taxonomy

This is a two-step process: first import marketplace category/attribute data from CSV, then build the taxonomy from that data.

### Step 1: Import Marketplace Attributes

**Endpoint**: `POST /marketplaces/attributes/import`
**File**: `src/backend/PdaEngine/Modules/Marketplaces/Endpoints/Write/ImportAttributesEndpoint.cs`

```
CSV Input Format:
┌────────────────────────────┬─────────────┬──────────────┐
│ Category                   │ Attribute   │ Value        │
├────────────────────────────┼─────────────┼──────────────┤
│ Computers & Accessories    │ item_name   │ Example Name │
│ Computers & Accessories    │ brand       │ Dell         │
│ Computers & Accessories    │ color_name  │ Black        │
│ Cell Phones                │ item_name   │ Phone Name   │
│ Cell Phones                │ brand       │ Apple        │
└────────────────────────────┴─────────────┴──────────────┘
```

**Processing Flow**:

```
1. CSV uploaded with marketplaceId
                ↓
2. Parse CSV rows (Category, Attribute, Value columns)
                ↓
3. Group by Category, then by Attribute
                ↓
4. For each unique Category:
   - Create/update MarketplaceCategory (with StateHash)
   - For each Attribute in category:
     - Create MarketplaceCategoryAttribute (Attribute, Value)
                ↓
5. Bulk insert using QuickInsertService (PostgreSQL COPY)
```

**Key Service**: Uses `QuickInsertService` for high-performance bulk inserts.

**Result**: `MarketplaceCategory` and `MarketplaceCategoryAttribute` tables populated with marketplace structure.

---

### Step 2: Build Marketplace Taxonomy

**Endpoint**: `POST /taxonomies/marketplace/build`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Endpoints/Write/BuildMarketplaceTaxonomyEndpoint.cs`

**Request Body**:
```json
{
  "marketplaceId": "AMAZON_US"
}
```

**Processing Flow**:

```
1. Validate marketplace exists
                ↓
2. Get/create MarketplaceTaxonomyMapping for marketplace
   - If exists: reuse existing mapping and taxonomy
   - If not: create new Taxonomy (MarketPlace type) and mapping
                ↓
3. Read from MarketplaceCategories table:
   - Get all categories for marketplace
   - Get all attributes for each category
                ↓
4. For each MarketplaceCategory:
   - Create TaxonomyCategory (same name)
   - For each MarketplaceCategoryAttribute:
     - Create TaxonomyCategoryAttribute
                ↓
5. Update state hashes
```

**Note**: This endpoint reads marketplace category data from `MarketplaceCategories` and `MarketplaceCategoryAttributes` tables populated by the import step. If a taxonomy already exists for the marketplace, it will be rebuilt.

---

## Workflow 4: Map Vendor Taxonomy → Marketplace Taxonomy

> **Note**: This workflow currently maps Vendor directly to Marketplace. When Internal Taxonomy is implemented, this will become Vendor → Internal mapping, with a separate Internal → Marketplace workflow.

### Create Taxonomy Mapping

**Endpoint**: `POST /taxonomy/mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Endpoints/Write/CreateTaxonomyMappingEndpoint.cs`

Creates the top-level link between two taxonomies.

### Create Category Mappings

**Endpoint**: `POST /taxonomy/category-mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Endpoints/Write/CreateTaxonomyCategoryMappingEndpoint.cs`

**Request**:
```json
{
  "taxonomyMappingId": 123,
  "sourceCategoryId": 456,
  "destinationCategoryId": 789,
  "attributeMappings": [
    { "sourceAttributeId": 1, "destinationAttributeId": 10 },
    { "sourceAttributeId": 2, "destinationAttributeId": 20 }
  ]
}
```

**Validations** (via `MappingValidator`):
1. TaxonomyMapping must exist
2. Source taxonomy cannot be MarketPlace type
3. Source category must belong to source taxonomy
4. Destination category must belong to destination taxonomy
5. All source attributes must exist in source category
6. All destination attributes must exist in destination category

---

## Workflow 5: Map Inventory → Taxonomy (Direct)

For mapping vendor categories directly to taxonomy without building a vendor taxonomy first.

**Endpoint**: `POST /taxonomy/inventory-category-mappings`
**File**: `src/backend/PdaEngine/Modules/Taxonomy/Endpoints/Write/CreateInventoryCategoryMappingEndpoint.cs`

**Request**:
```json
{
  "vendorId": "VENDOR001",
  "vendorCategory": "Electronics -> Computers",
  "taxonomyCategoryId": 456,
  "attributeMappings": [
    { "vendorAttribute": "Brand", "taxonomyCategoryAttributeId": 10 },
    { "vendorAttribute": "Color", "taxonomyCategoryAttributeId": 20 }
  ]
}
```

**Processing Flow**:

```
1. Get/create InventoryTaxonomyMapping for vendor
                ↓
2. Create InventoryTaxonomyCategoryMapping:
   - VendorCategory string → TaxonomyCategoryId
   - Initialize state hashes
                ↓
3. Create InventoryTaxonomyCategoryAttributeMapping for each:
   - VendorAttribute (string name) → TaxonomyCategoryAttributeId
                ↓
4. Update hashes via TaxonomyHashService
```

---

## Complete Data Flow

### Current Implementation (Direct Vendor → Marketplace)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           VENDOR LAYER                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  CSV Import → ProductCategory → VendorCategory → VendorCategoryAttribute│
│                ProductAttribute                                          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        VENDOR TAXONOMY                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  Taxonomy (Vendor) → TaxonomyCategory → TaxonomyCategoryAttribute       │
│                                                                          │
│  InventoryTaxonomyMapping ──┬── InventoryTaxonomyCategoryMapping        │
│                             └── InventoryTaxonomyCategoryAttributeMapping│
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ TaxonomyMapping (direct to Marketplace)
                                    │ TaxonomyCategoryMapping
                                    │ TaxonomyCategoryAttributeMapping
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      MARKETPLACE TAXONOMY                                │
├─────────────────────────────────────────────────────────────────────────┤
│  Taxonomy (MarketPlace) → TaxonomyCategory → TaxonomyCategoryAttribute  │
│                                                                          │
│  MarketplaceTaxonomyMapping ──┬── MarketplaceTaxonomyCategoryMapping    │
│                               └── MarketplaceTaxonomyCategoryAttributeMap│
└─────────────────────────────────────────────────────────────────────────┘
                                    ▲
                                    │
┌─────────────────────────────────────────────────────────────────────────┐
│                      MARKETPLACE DATA LAYER                              │
├─────────────────────────────────────────────────────────────────────────┤
│  CSV Import → MarketplaceCategory → MarketplaceCategoryAttribute        │
│  (Raw marketplace category/attribute structure from Amazon flat files)  │
└─────────────────────────────────────────────────────────────────────────┘
```

### Future Implementation (with Internal Taxonomy)

```
Vendor Taxonomy ──▶ Internal Taxonomy (Bison) ──▶ Marketplace Taxonomy
                         (FUTURE)
```

When Internal Taxonomy is implemented, it will serve as the canonical intermediate layer, allowing:
- Multiple vendors to map to a single internal structure
- One internal structure to map to multiple marketplaces
- Centralized attribute standardization

---

## State Hash Update Flow

After any mapping operation, state hashes are recalculated:

```csharp
// TaxonomyHashService.cs

// 1. Category hash = sorted attribute IDs
categoryHash = MD5(attributeIds.OrderBy(x => x).Join(","))

// 2. Mapping hash = source + destination
mappingHash = MD5(sourceStateHash + "|" + destinationStateHash)
```

This enables:
- Detecting when source/destination structures change
- Identifying mappings that need review after taxonomy changes
- Efficient querying via indexed hash columns
