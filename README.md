# Indix API NodeJS Client
This is an API client library for the Indix API written in NodeJS. For detailed documentation, please visit the [Indix Developer Portal](https://developer.indix.com/).

## Requirements
Node v5.0.0 or later

## Installation
Run the following command in your NodeJS project directory to download and install the API client.
```
npm install indix-api-nodejs --save
```

## Usage
The client needs to be first instantiated with the appropriate application id and key to be able to use the different api endpoints.
```js
var ixClient = require('indix-api-nodejs');
ixClient.init({
  appKey: 'APP_KEY'
});
```
Visit [developer.indix.com](the Indix developer portal) to get your own App ID and Key.

## Search for Brands, Stores or Categories
### Brands
```js
var query = { q: 'Nike' }
ixClient.getBrands(query).then(function(results){
  console.log(results[0]); // would print { id: 5572, name: 'Nike' }
});
```
### Stores
```js
var query = { q: 'Amazon.com' }
ixClient.getStores(query).then(function(results){
  console.log(results[0]); // would print { id: 270, name: 'Amazon.com Marketplace', countryCode: 'US' }
});
```
### Categories
*Note: This endpoint downloads all categories. This is not a search end point.*
```js
ixClient.getCategories().then(function(results){
  console.log(results[0]); // would print { id: 10161, idPath: "10161", name: 'Computers & Accessories', namePath: 'Computers & Accessories' }
});
```

## Search suggestions
```js
var query = { q: 'red', countryCode: 'US' }
ixClient.getSearchSuggestions(query).then(function(results){
  console.log(results[0]); // would be 'red'
});
```

## Search for products
```js
var query = { q: 'nike', countryCode: 'US' }
ixClient.getProductSummary(query).then(function(results){
  console.log(results[0]); // would print the product information of the first product
  console.log(results[0].mpid); // would print the mpid of the first product
});
```
List of all methods, each of which will return different types of product information.
```js
ixClient.getProductSummary();
ixClient.getProductOffersStandard();
ixClient.getProductOffersPremium();
ixClient.getProductCatalogStandard();
ixClient.getProductCatalogPremium();
ixClient.getProductUniversal();
```

## Lookup a specific product
```js
var query = { mpid: '8e1d8cd338ada38624d2f9322b259402', countryCode: 'US' }
ixClient.getProductLookupSummary(query).then(function(result){
  console.log(result); // would print the product information of the product
  console.log(result.mpid); // would print the mpid of the product
});
```
List of all methods, each of which will return different types of product information.
```js
ixClient.getProductLookupSummary();
ixClient.getProductLookupOffersStandard();
ixClient.getProductLookupOffersPremium();
ixClient.getProductLookupCatalogStandard();
ixClient.getProductLookupCatalogPremium();
ixClient.getProductLookupUniversal();
```

## Bulk processing
The API supports processing a large volume of products at once. This is an asynchronous process and requires a 3 step process:

1. Submit a query, which returns a `Job ID`.
2. Poll for the job status until it is complete.
3. Once complete, download the products.

## Bulk search for products
### Submit a query
```js
var query = { storeId: [68], countryCode: 'US' }
ixClient.getBulkProductSummary(query).then(function(jobInfo){
  console.log(jobInfo); // would be like { id: 1234, status: 'SUBMITTED' }
});
```

List of all methods, each of which will return different types of product information.
```js
ixClient.getBulkProductSummary();
ixClient.getBulkProductOffersStandard();
ixClient.getBulkProductOffersPremium();
ixClient.getBulkProductCatalogStandard();
ixClient.getBulkProductCatalogPremium();
ixClient.getBulkProductUniversal();
```

### Poll for job status
```js
ixClient.getJobStatus(1234).then(function(jobInfo){
  console.log(jobInfo); // would be like { id: 1234, status: 'COMPLETED', count: 10000 }
});
```

### Download the products
```js
ixClient.downloadProducts(1234).then(function(results){
  console.log(results[0]); // would print the product information of the first product
  console.log(results[0].mpid); // would print the mpid of the first product
});
```

## Bulk lookup specific set of products
### Submit a query
```js
var query = {
  inputFile: fs.createReadStream('test/stubs/bulk-job-input.txt', 'utf8'), // Refer to examples below for format
  countryCode: 'US'
}
ixClient.getBulkProductLookupSummary(query).then(function(jobInfo){
  console.log(jobInfo); // would be like { id: 1234, status: 'SUBMITTED' }
});
```

List of all methods, each of which will return different types of product information.
```js
ixClient.getBulkProductLookupSummary();
ixClient.getBulkProductLookupOffersStandard();
ixClient.getBulkProductLookupOffersPremium();
ixClient.getBulkProductLookupCatalogStandard();
ixClient.getBulkProductLookupCatalogPremium();
ixClient.getBulkProductLookupUniversal();
```

### Input File Formats
#### MPN with Brand ID
```
{ "mpn": "1281HTWR", "brandId": 28860 }
{ "mpn": "5121412LMR", "brandId": 6553 }
{ "mpn": "S-19141SIL", "brandId": 2579 }
{ "mpn": "2000157819", "brandId": 48162 }
```

#### SKU with Store ID
```
{ "sku": "228-10245", "storeId": 311 }
{ "sku": "228-10255", "storeId": 311 }
{ "sku": "29G-00752", "storeId": 311 }
{ "sku": "29G-00754", "storeId": 311 }
{ "sku": "29G-01030", "storeId": 311 }
```

### UPC
```
{ "upc": 80196294216 }
{ "upc": 92325000018 }
{ "upc": 844660084877 }
{ "upc": 14389642713 }
{ "upc": 14389642720 }
```

### Polling for job and downloading products
This is exactly the same as mentioned in the `Bulk search for products` section. Please refer to that above.
