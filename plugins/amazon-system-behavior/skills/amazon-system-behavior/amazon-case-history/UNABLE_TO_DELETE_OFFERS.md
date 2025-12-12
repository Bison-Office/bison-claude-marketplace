<case_id>19079285911</case_id>

<question>
Hello, - we have 13 offer examples that seems to be returned from reports, but do not seem to actually exist.


GET_FLAT_FILE_OPEN_LISTINGS_DATA Report returns ASINS that are deleted, which is indicated by calling `https://sellingpartnerapi-na.amazon.com/products/pricing/v0/items/{Asin}/offers` endpoint, for all of them we get a bad request error:

```json
{
	"errors": [
	    {
	        "code": "InvalidInput",
	        "message": "{ASIN} is an invalid ASIN for marketplace ATVPDKIKX0DER",
	        "details": ""
	    }
	]
}
```

These asins are also not visible in amazon seller central

Could you clarify if that’s expected / a bug on amazon api’s or there’s something we’re doing wrong?

Asins in question (asin, our sku)

- B004E3K87Y, BSHWC24412
- B004E3LBGQ , BSHWC24414
- B005HGD9A0 , SAN63721
- B008ORU8YE, FEL9176501
- B00A4RZN4M, AVE11907,
- B00BGX3SKY, LCI2053
- B00DMG7OUI, CASPOL2811
- B00OCVU2SS , CD-0624
- B00QSAGUJQ, PAC56225
- B00UWK88MA , BOSEPS8HDBLK
- B06XTZDYXN, WX16563
- B071K5XYNW, CD-4402
- B073SDZTVQ, CD-0636


What steps have you taken already?
We tried to call an api for each of the asins, - did receive that offer does not exist for all 13 asins.

ASIN: B004E3K87Y, BSHWC24412, B004E3LBGQ, BSHWC24414, B005HGD9A0, B008ORU8YE, B00A4RZN4M, B00BGX3SKY, B00DMG7OUI, B00OCVU2SS, B00QSAGUJQ, B00UWK88MA, B06XTZDYXN, B071K5XYNW, B073SDZTVQ
</question>

<response>
Greetings from Amazon,

My name is Mahima, from the Developer Support team.

I understand you contacted us as few ASINs are reflecting in GET_FLAT_FILE_OPEN_LISTINGS_DATA Report but are actually not present pricing API or in the seller central.

This behavior is expected and reflects how Amazon's inventory reporting and pricing systems work at different levels of the catalog hierarchy.

Why This Happens

The GET_FLAT_FILE_OPEN_LISTINGS_DATA report and the Product Pricing API serve different purposes and operate on different data:

Inventory Reports (GET_FLAT_FILE_OPEN_LISTINGS_DATA)

- Returns listings from your seller inventory 
- Shows items with SKU-to-ASIN mappings that exist in your account
- Includes listings that may reference ASINs that are no longer active in Amazon's catalog
- The report specifically returns items "with the price and quantity for each SKU"

Product Pricing API

- Operates on the Amazon product catalog level 
- Requires ASINs to be active and valid in the marketplace catalog
- Returns pricing and offer information for currently active products
- Will return "InvalidInput" errors for ASINs that don't exist in the catalog

What's Happening with Your Listings
Your seller account maintains SKU-to-ASIN mappings even after ASINs are removed from Amazon's catalog. These "orphaned" listings:

1. Still appear in inventory reports because they exist in your seller inventory data
2. Cannot be queried via Pricing API because the ASINs no longer exist in the marketplace catalog
3. Don't appear in Seller Central because they reference deleted catalog items

Recommended Solutions

1. Identify Inactive Listings- Use the Inactive Listings Report to identify problematic listings:
reportType: GET_MERCHANT_LISTINGS_INACTIVE_DATA

This report "includes listings that have zero (0) quantity, listings that are blocked, listings that are suppressed, listings that are not yet opened, and listings that are past their sale end date".

Understanding Amazon listing status and seller-fulfilled inventory management: https://developer-docs.amazon.com/sp-api/docs/understanding-amazon-listing-status-and-seller-fulfilled-inventory-management

2. Check Listing Status via Listings Items API- Instead of the Pricing API, use the Listings Items API to check individual listings:
GET /listings/2021-08-01/items/{sellerId}/{sku}

This will return the actual status of your listing and any issues, including whether the ASIN is invalid.

3. Monitor with Notifications- Subscribe to listing status notifications to catch these issues proactively:

- LISTINGS_ITEM_STATUS_CHANGE - Notifies when listing status changes 
- LISTINGS_ITEM_ISSUES_CHANGE - Alerts you to listing issues 

4. Clean Up Your Inventory- Delete these orphaned listings using the Listings Items API:
DELETE /listings/2021-08-01/items/{sellerId}/{sku}

"Deleting a listing completely removes it from the seller inventory. It does not appear in any of the reports or API data".
</response>