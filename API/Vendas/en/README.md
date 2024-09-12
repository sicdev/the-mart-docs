# Sales API

Here you will find an overview of the sales API and how to integrate it into your application.

## Index

- [Authentication](#authentication)
  - [How to Generate an Access Token](#how-to-generate-an-access-token)
- [Get Sales](#get-sales-get-integrationsales)
  - [Query Params](#query-params-sales)
  - [Response Example](#response-example-sales)
  - [Response Types](#response-types-sales)
    - [Sale](#sale)
    - [Customer](#customer)
    - [Product](#product)
- [Get Products](#get-products-get-integrationproducts)
  - [Query Params](#query-params-products)
  - [Response Example](#response-example-products)
  - [Response Types](#response-types-products)
- [Alterar Código de Rastreio](#alterar-código-de-rastreio-patch-integrationsale-shipping)
  - [Payload](#payload-rastreio)
  - [Resposta](#resposta-rastreio)

## Authentication

Authentication for all routes is done using the `Authorization` header, utilizing a Bearer Token.
To use a route, it is necessary to generate a user access token.

### How to Generate an Access Token

1. Go to the API menu under the _Ferramentas_ submenu in the side menu.
2. Click on the (+) button in the upper right corner.
3. Give it a nickname to make it easier to identify the new token and click _Salvar_.
4. Voilà, your token has been generated, and you can copy it using the button with the :link: icon on the right side of the table.

## Get Sales (`GET /integration/sales`)

Use this route to list the sales of the authenticated user, applying filters for status, date, pagination, and sorting. Providing the date range (`startDate` and `endDate`) is required.

```bash
curl --request GET \
  --url 'https://the-mart-api.herokuapp.com/integration/sales?startDate=2024-01-01T00:00:00.000Z&endDate=2024-02-01T00:00:00.000Z&orderBy=productName&sort=asc' \
  --header 'Accept: application/json, text/plain, */*' \
  --header 'Authorization: Bearer {Bearer Token}' \
```

### Query Params (Sales)

| Field       | Example Value                  | Description                                                                                             |
| ----------- | ------------------------------ | ------------------------------------------------------------------------------------------------------- |
| `startDate` | `"2024-01-09T00:00:00.000Z"`   | Date ISO 8601                                                                                           |
| `endDate`   | `"2024-01-09T23:59:59.999"`    | Date ISO 8601                                                                                           |
| `status`    | `"success" \| "pending"`       | Sale Status                                                                                             |
| `orderBy`   | `"createdAt" \| "productName"` | The default value is `createdAt`                                                                        |
| `sort`      | `"asc" \| "desc"`              | The default value is `asc`                                                                              |
| `page`      | `1`                            | Pagination page                                                                                         |
| `quantity`  | `10`                           | Number of items per page when performing pagination. The default value is 10. The maximum value is 100. |

### Response Example (Sales)

```JSON
{
   "totalCount":1,
   "results":[
      {
         "sale":{
            "saleId":"or_xxxxxxxx",
            "orderUuid": "tmor_xxxxxxxxxx",
            "TotalCheckout":20,
            "createdAt":"2024-01-12T13:38:19.314Z",
            "updatedAt":"2024-01-12T13:38:29.272Z",
            "status":"success",
            "totalProducts":20,
            "totalFrete": 0,
            "discount": 0,
            "productQty": 1,
            "paymentMethod": "credit_card"
         },
         "customer":{
            "customerId":"cus_xxxxxxx",
            "customerName":"Customer Name",
            "customerDocument":"33199554479",
            "customerEmail":"customeremail@teste.com",
            "customerPhone":"+5599999999999",
            "address":"Test Street",
            "zipcode":"89033259",
            "number":"999",
            "complement":"Complement 123",
            "neighborhood":"Velha",
            "city":"Blumenau",
            "state":"SC",
            "country":"BR"
         },
         "products":[
            {
               "ProductID":"themartcc1977-ddfe-4180-bcf9-843819970512",
               "productName":"Product Name",
               "productUnitaryValue":20,
            }
         ]
      }
   ]
}
```

### Response Types (Sales)

| Properties   | Type      | Description                  |
| ------------ | --------- | ---------------------------- |
| `totalcount` | Number    | Total count of records found |
| `results`    | Object[]  | Records found                |
| `sale`       | Sale      | Sale information             |
| `customer`   | Customer  | Customer information         |
| `products`   | Product[] | Product information          |

#### Sale

| Properties      | Type            | Description               |
| --------------- | --------------- | ------------------------- |
| `saleId`        | String          | Unique Sale identifier    |
| `orderUuid`     | String          | Unique Order identifier   |
| `TotalCheckout` | Number          | Sale total value          |
| `totalProducts` | Number          | Products aggregated value |
| `status`        | String          | Sale Status               |
| `paymentMethod` | PaymentMethod   | Sale Payment Method       |
| `totalFrete`    | Number          | Shipping value            |
| `discount`      | Number          | Discount value            |
| `productQty`    | Number          | Product quantity          |
| `createdAt`     | ISO 8601 String | Sale creation date        |
| `updatedAt`     | ISO 8601 String | Last sale update date     |

PaymentMethod = `'credit_card'` | `'boleto'` | `'pix'`

#### Customer

| Properties         | Type   | Description                |
| ------------------ | ------ | -------------------------- |
| `customerId`       | String | Customer unique identifier |
| `customerName`     | String | Name                       |
| `customerDocument` | String | Document                   |
| `customerEmail`    | String | Email                      |
| `customerPhone`    | String | Phone                      |
| `address`          | String | Address                    |
| `zipcode`          | String | Zipcode                    |
| `number`           | String | Number                     |
| `complement`       | String | Complement                 |
| `neighborhood`     | String | Neighborhood               |
| `city`             | String | City                       |
| `state`            | String | State                      |
| `country`          | String | Country                    |

#### Product

| Properties            | Type   | Description               |
| --------------------- | ------ | ------------------------- |
| `ProductID`           | String | Product unique identifier |
| `productName`         | String | Product name              |
| `productUnitaryValue` | Number | Unitary value             |

## Get Products (`GET /integration/products`)

Use this route to list the products of the authenticated user, applying pagination and sorting filters.

```bash
curl --request GET \
  --url https://the-mart-api.herokuapp.com/integration/products \
  --header 'Accept: application/json, text/plain, */*' \
  --header 'Authorization: Bearer {Bearer Token}'
```

### Query Params (Products)

| Field      | Example Value           | Description                                                                                             |
| ---------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| `orderBy`  | `"createdAt" \| "name"` | The default value is `createdAt`                                                                        |
| `sort`     | `"asc" \| "desc"`       | The default value is `asc`                                                                              |
| `page`     | `1`                     | Pagination Page                                                                                         |
| `quantity` | `10`                    | Number of items per page when performing pagination. The default value is 10. The maximum value is 100. |

### Response Example (Products)

```JSON
{
   "totalCount":2,
   "results":[
      {
         "ProductID":"xxxxxxxx-ddfe-1997-bcf9-843838530101",
         "productName":"Product Name"
      },
      {
         "ProductID":"xxxxxxxx-ddfe-1997-bcf9-843838530202",
         "productName":"Product Name 2"
      }
   ]
}
```

### Response Types (Products)

| Properties    | Type     | Description                  |
| ------------- | -------- | ---------------------------- |
| `totalcount`  | Number   | Total count of records found |
| `results`     | Object[] | Records found                |
| `ProductID`   | String   | Product unique identifier    |
| `productName` | String   | Product name                 |

## Update Sale Tracking Info (`PATCH /integration/sale-shipping`)

Use this route to update or insert the tracking code and logistics provider for a sale.

```bash
curl -X 'PATCH' \
  'http://the-mart-api.herokuapp.com/integration/sale-shipping' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer {Bearer Token}' \
  -H 'Content-Type: application/json' \
  -d '{
  "data": [
    {
      "saleId": "tmor_...",
      "shippingService": "correios",
      "shippingCode": "QR...BR"
    }
  ]
}'
```

## Payload (Tracking)

| Propriedades      | Tipo     | Descrição                                 |
| ----------------- | -------- | ----------------------------------------- |
| `data`            | Object[] | Sales to be updated and its data          |
| `saleId`          | String   | Sale unique identificator (`tmor_...`)    |
| `shippingService` | String   | Logistics service (correios, jadlog, etc) |
| `shippingCode`    | String   | Tracking code                             |

## Response (Tracking)

The response consists of control arrays of the provided IDs, listing the successful ones, the ones that caused failure, and the ones that were not found.

| Propriedades        | Tipo     | Descrição                     |
| ------------------- | -------- | ----------------------------- |
| `successfulUpdates` | String[] | Successfully updated sale IDs |
| `failedUpdates`     | String[] | Failed to update sale IDs     |
| `notFoundOrders`    | String[] | Not found sale IDs            |
