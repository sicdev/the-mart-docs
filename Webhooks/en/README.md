# Webhooks

Here you will find an overview of webhooks and how to integrate them into your application.

## Index

- [Available Trigger Events](#available-trigger-events)
- [Authentication](#authentication)
- [Payload](#payload)
- [Types](#data)
  - [WebhookBody](#webhookbody)
  - [Product](#product)
  - [Plan](#plan)
  - [Order](#order)
  - [PaymentMethods](#payment-methods)
  - [Status](#status)
  - [PixPayment](#pixpayment)
  - [BoletoPayment](#boletopayment)
  - [Shipping](#shipping)
  - [Customer](#customer)
  - [Address](#address)
  - [Phone](#phone)

## Available Trigger Events

| Event Name           | Trigger              |
| -------------------- | -------------------- |
| `abandoned-checkout` | Checkout abandonment |
| `order-made`         | Order made           |
| `order-paid`         | Order paid           |
| `order-refunded`     | Order refunded       |
| `shipping-updated`   | Shipping Updated     |

## Authentication

The webhook method does not require any extra authentication. It is generated individually, allowing only information belonging to the generator to be sent.

## Payload

```JSON
{
  "webhookUuid": "tmwh_Z2q62uvucp",
  "name": "Webhook Name",
  "url": "https://exemplo.com",
  "type": "shipping-updated",
  "version": "1.3",
  "data": {
    "product": {
      "uuid": "99f1a886-d2b8-4e48-ae63-11e0a5bd9887",
      "name": "Product Name",
      "description": "Product description",
      "createdAt": "2024-02-19T18:44:19.741Z",
      "updatedAt": "2024-02-23T20:11:24.347Z"
    },
    "plan": {
      "uuid": "tmpl_GUR2aM7YJR",
      "description": "Plan Name",
      "price": 154,
      "commission": 14,
      "createdAt": "2024-02-19T18:46:38.981Z",
      "updatedAt": "2024-02-23T20:05:31.043Z"
    },
    "order": {
      "uuid": "tmor_R-ZRKe1QTF",
      "planValue": 154,
      "installments": null,
      "paymentMethod": "boleto",
      "payment": {
        "barcodeUrl": "https://api.pagar.me/core/v5/transactions/.../barcode",
        "boletoUrl": "https://pagar.me/...",
        "dueAt": "2024-02-26T23:59:59Z",
        "line": "1234 5678",
        "pdfUrl": "https://pagar.me/pdf/...",
        "qrCodeUrl": "https://api.pagar.me/core/v5/transactions/.../qrcode"
      },
      "status": "success",
      "shipping": {
        "shippingCode": "codigo.20.24",
        "shippingService": "correios",
      },
      "createdAt": "2024-02-23T20:49:14.777Z",
      "updatedAt": "2024-02-23T20:49:14.777Z"
    },
    "customer": {
      "name": "Customer Name",
      "email": "customeremail@test.com",
      "type": "individual",
      "document": "99999999999",
      "documentType": "cpf",
      "address": {
        "city": "Blumenau",
        "state": "SC",
        "country": "BR",
        "zipCode": "89045050",
        "line_1": "999, Rua A, Bairro B",
        "line_2": "Complemento 123"
      },
      "phone": {
        "countryCode": "55",
        "number": "999999999",
        "areaCode": "99"
      }
    }
  }
}
```

### Data

| Properties    | Type          | Description                    |
| ------------- | ------------- | ------------------------------ |
| `webhookUuid` | String        | Webhook unique identifier      |
| `name`        | String        | Webhook nickname               |
| `url`         | String        | URL called by the webhook      |
| `type`        | EventTriggers | Webhook trigger event          |
| `version`     | String        | Payload version                |
| `data`        | WebhookBody   | Content of the webhook payload |

### WebhookBody

:warning: When triggered by the **'abandoned-checkout'** event, the property `order` will be **null** due to the unfinished checkout process.

| Properties | Type     | Description             |
| ---------- | -------- | ----------------------- |
| `product`  | Product  | Content of the Product  |
| `plan`     | Plan     | Content of the Plan     |
| `order`    | Order    | Content of the Order    |
| `customer` | Customer | Content of the Customer |

### Product

| Properties    | Type            | Description               |
| ------------- | --------------- | ------------------------- |
| `uuid`        | String          | Product unique identifier |
| `name`        | String          | Product name              |
| `description` | String          | Product description       |
| `createdAt`   | ISO 8601 String | Product creation date     |
| `updatedAt`   | ISO 8601 String | Last Product update date  |

### Plan

| Properties    | Type            | Description                                          |
| ------------- | --------------- | ---------------------------------------------------- |
| `uuid`        | String          | Plan unique identifier                               |
| `description` | String          | Plan name                                            |
| `price`       | Number          | Plan value                                           |
| `commission`  | Number          | Plan commission percentage (value `10` equals `10%`) |
| `createdAt`   | ISO 8601 String | Plan creation date                                   |
| `updatedAt`   | ISO 8601 String | Last Plan update date                                |

### Order

In the **'order-made'** and **'order-paid'** events for orders placed with _Boleto_ or _PIX_, the property payment will be filled with the necessary data to complete the order payment, otherwise it will be **null**.
The `shipping` property will be **null** by default, however, when updated, the **'shipping-updated'** event will be triggered.

| Properties      | Type                          | Description                       |
| --------------- | ----------------------------- | --------------------------------- |
| `uuid`          | String                        | Order unique identifier           |
| `planValue`     | Number                        | Plan value                        |
| `installments`  | Number?                       | Installments count                |
| `paymentMethod` | PaymentMethods                | Payment method                    |
| `payment`       | PixPayment? \| BoletoPayment? | Data used to complete the payment |
| `status`        | Status                        | Order status                      |
| `shipping`      | Shipping                      | Shipping data                     |
| `createdAt`     | ISO 8601 String               | Order creation date               |
| `updatedAt`     | ISO 8601 String               | Last sale update date             |

### Payment Methods

| Valor         | Description |
| ------------- | ----------- |
| `pix`         | _PIX_       |
| `boleto`      | _Boleto_    |
| `credit_card` | Credit Card |

### Status

| Value       | Description                        |
| ----------- | ---------------------------------- |
| `error`     | Error during the Sale (or Payment) |
| `pending`   | Payment pending                    |
| `success`   | Sale made (Payment Successful)     |
| `refunded`  | Sale refunded                      |
| `free-paid` | Sale made (Free plan)              |

### PixPayment

| Properties  | Type            | Description                    |
| ----------- | --------------- | ------------------------------ |
| `qrCode`    | String          | QR Code URL                    |
| `qrCodeUrl` | String          | QR Code image URL              |
| `expiresAt` | ISO 8601 String | Expiration date of the QR Code |

### BoletoPayment

| Properties   | Type            | Description                                   |
| ------------ | --------------- | --------------------------------------------- |
| `barcodeUrl` | String          | Barcode URL                                   |
| `boletoUrl`  | String          | _Boleto_ URL                                  |
| `dueAt`      | ISO 8601 String | Data de Vencimento do Boleto                  |
| `line`       | String          | Barcode digits                                |
| `pdfUrl`     | String          | _Boleto_ PDF URL                              |
| `qrcodeUrl`  | String          | URL to the QR Code image for _Boleto_ payment |

### Shipping

| Properties        | Type   | Description                      |
| ----------------- | ------ | -------------------------------- |
| `shippingCode`    | String | Tracking code                    |
| `shippingService` | String | Company responsible for shipping |

### Customer

| Properties | Type    | Description       |
| ---------- | ------- | ----------------- |
| `name`     | String  | Customer Name     |
| `email`    | String  | Customer Email    |
| `type`     | String  | Document Type     |
| `document` | String  | Customer Document |
| `address`  | Address | Customer Address  |
| `phone`    | Phone   | Customer Phone    |

### Address

When triggered by the **'abandoned-checkout'** event, the properties of `Address` are optional and may return as empty strings. In some cases where the address is not provided, `Address` will return **null**.

| Properties | Type   | Description        |
| ---------- | ------ | ------------------ |
| `city`     | String | City               |
| `state`    | String | State Abbreviation |
| `country`  | String | Country            |
| `zipCode`  | String | Zipcode            |
| `line_1`   | String | Street Address     |
| `line_2`   | String | Complement         |

### Phone

| Properties    | Type   | Description  |
| ------------- | ------ | ------------ |
| `countryCode` | String | Country Code |
| `number`      | String | Phone number |
| `areaCode`    | String | Area Code    |
