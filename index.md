# Tech Farben Enterprise Bridge

| Name | eCommerce Sync API Guide |
| Document Version | 1.0 |
| Version Date | July 27, 2022 |


# Purpose
This guide is to be used when developers want to integrate their e-commerce platform with Tech Farben's Enterprise Bridge to synchronize data between an e-commerce platform like WooCommerce or Shopify and an ERP solution like NetSuite or SAP Hana.

# URLs

| Environment | URL |
| ---- | ---- |
| Staging / Sandbox | https://eb-stage.techfarben.com |
| Live | https://eb-live.techfarben.com |

# Access Setup
Contact your relationship manager at TechFarben to setup your access for Sandbox. Your team will work against the sandbox during the development phase. Upon completion of the development, there will be a formal review of the implementation with the TechFarben's team. Based on the outcome of the review, you will be either provided with Live access or supplied with points that need to addressed before granting live access.

# Authentication
Your access setup email will contain a token which needs to supplied with every request via the HTTP `Authorization` header as follows:

```http
Authorization: Bearer <REPLACE_WITH_TOKEN>
```

# API
| Item | Value |
| ---- | ---- |
| Endpoint | /api/v1/sync/ |
| Supported HTTP Methods | POST |
| Content-Type Accepted | application/json |

Call the above API with JSON data specifying the transaction type and data associated with each transaction in the request body. Top-level structure of the request body:

```json
{
	"TransactionType": "",
	"TransactionData": {
		...
	}
}
```

A successful `HTTP/200 Ok` API response will look like:
```json
{
	"TransactionType": "",
	"TransactionStatus": "",
	"TransactionId": "",
}
```

In case of validation errors or other errors, you the response will be `HTTP/400 Bad Request` and the top level structure will be
```json
{
	"TransactionType": "",
	"TransactionStatus": "BadRequest",
	"TransactionId": "",
	"Notes": {
		"FieldName": [] // List of issues 
	}
}
```

The following transaction types are supported:

| Transaction Type | Description | Documentation |  
|---|---|---|
| UserRegister | Synchronize a new user | [Link](#userregister) |  
| UserUpdate | Update user data like name, email etc. | [Link](#userupdate) |
| NewSale | Create a new sale record | [Link](#newsale) |
| CancelSale | Cancel a previous sale | [Link](#cancelsale) |


## UserRegister
Use this API when a new user registers with the eCommerce system.

Request Example:
```
https://eb-stage.techfarben.com/api/v1/sync/

Authorization: Bearer <YOUR_ACCESS_TOKEN>
Content-Type: application/json
```

```json
{
	"TransactionType": "UserRegister",
	"TransactionData": {
		"SystemObjectId": "The ID assigned to the user by the eCommerce system",
		"FirstName": "Max 255 characters",
		"MiddleName": "Max 255 characters - Optional",
		"LastName": "Max 255 characters - Optional",
		"DateOfBirth": "YYYY-MM-DD - Optional",
		"Address": "Max 255 characters - Optional",
		"CountryCode": "Valid 2-digit ISO Country Code - Optional",
		"PinCode": "A valid pin code for the selected country - Optional",
	}
}
```

## UserUpdate
Use this API when the data of an existing user changes with the eCommerce system.
The user to synchronize will be matched against the supplied `SystemObjectId`.

Request Example:
```
https://eb-stage.techfarben.com/api/v1/sync/

Authorization: Bearer <YOUR_ACCESS_TOKEN>
Content-Type: application/json
```

```json
{
	"TransactionType": "UserUpdate",
	"TransactionData": {
		"SystemObjectId": "The ID assigned to the user by the eCommerce system",
		"FirstName": "Max 255 characters",
		"MiddleName": "Max 255 characters - Optional",
		"LastName": "Max 255 characters - Optional",
		"DateOfBirth": "YYYY-MM-DD - Optional",
		"Address": "Max 255 characters - Optional",
		"CountryCode": "Valid 2-digit ISO Country Code - Optional",
		"Pincode": "A valid pincode for the selected country - Optional",
	}
}
```

## NewSale
Use this API when a new sale is recorded with the eCommerce system.

Request Example:
```
https://eb-stage.techfarben.com/api/v1/sync/

Authorization: Bearer <YOUR_ACCESS_TOKEN>
Content-Type: application/json
```

```json
{
	"TransactionType": "NewSale",
	"TransactionData": {
		"SystemObjectId": "The ID assigned to the sale by the eCommerce system",
		"SystemUserObjectId": "The ID assigned to the user (customer) by the eCommerce system",
		"SoldInventory": {
			"Name": "",
			"ExtraData": {}, // A json object containing arbitrary pair of key and values of the data associated with the sale and not supplied elsewhere.
		},
		"TransactionValue": 123.24, // This is the amount charged to the user
		"Currency": "3-letter ISO currency code of the currency used for the transaction. Ex. SGD, USD, INR, EUR etc.",
		"TransactionStructure": {
			"BaseAmount": "Amount before discounts, taxes, fees-charged and fees-paid (if any) applied",
			"Discounts": [{
				"Name": "Name of the discount",
				"Value": "Value of the discount in the same currency as the transaction value"
			}], // Can be multiple
			"Taxes": [{
				"Name": "Name of the tax",
				"Value": "Value of the taxes in the same currency as the transaction value",
				"TaxCode": "Like GSTIN etc. - Optional"
			}], // Can be multiple
			"FeeCharged": [{ // This is the fee that is charged to the customer like convenience fee
				"Name": "Name of the fee",
				"Value": "Value of the fee in the same currency as the transaction value"
			}], // Can be multiple
			"FeePaid": [{ // This is the fee that is borne by the eCommerce platform owner like payment-gateway charges
				"Name": "Name of the fee",
				"Value": "Value of the fee in the same currency as the transaction value"
			}] // Can be multiple
		}
	}
}
```

## CancelSale
Use this API when a previous sale is canceled with the eCommerce system.
The sale to cancel will be matched against the supplied `SystemObjectId`.

Request Example:
```
https://eb-stage.techfarben.com/api/v1/sync/

Authorization: Bearer <YOUR_ACCESS_TOKEN>
Content-Type: application/json
```

```json
{
	"TransactionType": "CancelSale",
	"TransactionData": {
		"SystemObjectId": "The ID assigned to the original sale by the eCommerce system",
		"CancellationStructure": {
			"BaseAmount": "Amount before discounts, taxes, fees-charged and fees-paid (if any) applied",
			"Discounts": [{
				"Name": "Name of the discount",
				"Value": "Value of the discount in the same currency as the transaction value"
			}], // Can be multiple
			"Taxes": [{
				"Name": "Name of the tax",
				"Value": "Value of the taxes in the same currency as the transaction value",
				"TaxCode": "Like GSTIN etc. - Optional"
			}], // Can be multiple
			"FeeCharged": [{ // This is the fee that is charged to the customer like convenience fee
				"Name": "Name of the fee",
				"Value": "Value of the fee in the same currency as the transaction value"
			}], // Can be multiple
			"FeePaid": [{ // This is the fee that is borne by the eCommerce platform owner like payment-gateway charges
				"Name": "Name of the fee",
				"Value": "Value of the fee in the same currency as the transaction value"
			}] // Can be multiple
		}
	}
}
```
