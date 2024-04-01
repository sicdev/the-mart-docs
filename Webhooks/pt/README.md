# Webhooks

Aqui você encontrará uma visão geral dos webhooks e como integrá-los à sua aplicação.

## Índice

- [Eventos Disponíveis](#eventos-disponíveis)
- [Autenticação](#autenticação)
- [Payload](#payload)
- [Tipos](#tipos)
  - [WebhookBody](#webhookbody)
  - [Product](#product)
  - [Plan](#plan)
  - [Order](#order)
  - [PaymentMethods](#payment-methods)
  - [Status](#status)
  - [PixPayment](#pixpayment)
  - [BoletoPayment](#boletopayment)
  - [Customer](#customer)
  - [Address](#address)
  - [Phone](#phone)

## Eventos disponíveis

| Nome do Evento       | Momento de Ativação  |
| -------------------- | -------------------- |
| `abandoned-checkout` | Abandono do checkout |
| `order-made`         | Pedido realizado     |
| `order-paid`         | Pedido pago          |
| `order-refunded`     | Pedido estornado     |
| `shipping-updated`   | Envio atualizado     |

## Autenticação

O método de webhooks dispensa qualquer autenticação extra. Sendo gerada individualmente e possibilitando envio somente de informações pertencentes a quem o gerou.

## Payload

```JSON
{
  "webhookUuid": "tmwh_Z2q62uvucp",
  "name": "Webhook Name",
  "url": "https://exemplo.com",
  "type": "shipping-updated",
  "version": "1.2",
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

### Tipos

| Propriedade   | Tipo          | Descrição                            |
| ------------- | ------------- | ------------------------------------ |
| `webhookUuid` | String        | Identificador único do Webhook       |
| `name`        | String        | Apelido dado ao Webhook              |
| `url`         | String        | URL do endpoint ativado pelo Webhook |
| `type`        | EventTriggers | Evento de disparo do Webhook         |
| `version`     | String        | Versão atual do Payload              |
| `data`        | WebhookBody   | Conteúdo do Webhook                  |

### WebhookBody

:warning: Quando ativado pelo evento **'abandoned-checkout'** a propriedade `order` será nula, devido ao processo de compra inacabado.

| Propriedade | Tipo     | Descrição           |
| ----------- | -------- | ------------------- |
| `product`   | Product  | Conteúdo do Produto |
| `plan`      | Plan     | Conteúdo do Plano   |
| `order`     | Order    | Conteúdo do Pedido  |
| `customer`  | Customer | Dados do Cliente    |

### Product

| Propriedade   | Tipo            | Descrição                      |
| ------------- | --------------- | ------------------------------ |
| `uuid`        | String          | Identificador único do Produto |
| `name`        | String          | Nome do Produto                |
| `description` | String          | Descrição do Produto           |
| `createdAt`   | ISO 8601 String | Data de criação do Produto     |
| `updatedAt`   | ISO 8601 String | Ultima atualização do Produto  |

### Plan

| Propriedade   | Tipo            | Descrição                                                     |
| ------------- | --------------- | ------------------------------------------------------------- |
| `uuid`        | String          | Identificador único do Plano                                  |
| `description` | String          | Nome do Plano                                                 |
| `price`       | Number          | Valor do Plano                                                |
| `commission`  | Number          | Comissão do Plano em porcentagem (Valor `10` significa `10%`) |
| `createdAt`   | ISO 8601 String | Data de criação do Plano                                      |
| `updatedAt`   | ISO 8601 String | Ultima atualização do Plano                                   |

### Order

Nos eventos **'order-made'** e **'order-paid'** em pedidos realizados com Boleto ou Pix, a propriedade `payment` será preenchida com os dados necessários para realizar o pagamento do pedido, do contrário, ela será **nula**.
A propriedade `shipping` será **nula** por padrão, porém ao ser atualizada será disparado o evento **'shipping-updated'**.

| Propriedade     | Tipo                          | Descrição                                             |
| --------------- | ----------------------------- | ----------------------------------------------------- |
| `uuid`          | String                        | Identificador único da Venda                          |
| `planValue`     | Number                        | Valor do Plano                                        |
| `installments`  | Number?                       | Número de Parcelas                                    |
| `paymentMethod` | PaymentMethods                | Método de Pagamento                                   |
| `payment`       | PixPayment? \| BoletoPayment? | Dados necessários para realizar o pagamento do Pedido |
| `status`        | Status                        | Status do Pedido                                      |
| `shipping`      | Shipping                      | Dados do Envio                                        |
| `createdAt`     | ISO 8601 String               | Data de criação da Venda                              |
| `updatedAt`     | ISO 8601 String               | Ultima atualização da Venda                           |

### Payment Methods

| Valor         | Descrição         |
| ------------- | ----------------- |
| `pix`         | PIX               |
| `boleto`      | Boleto            |
| `credit_card` | Cartão de Crédito |

### Status

| Valor      | Descrição                       |
| ---------- | ------------------------------- |
| `error`    | Erro na Venda (ou no Pagamento) |
| `pending`  | Pagamento Pendente              |
| `success`  | Venda Concluída (Paga)          |
| `refunded` | Venda Estornada                 |

### PixPayment

| Propriedade | Tipo            | Descrição                     |
| ----------- | --------------- | ----------------------------- |
| `qrCode`    | String          | Link do QR Code               |
| `qrCodeUrl` | String          | Link da imagem do QR Code     |
| `expiresAt` | ISO 8601 String | Data de Vencimento do QR Code |

### BoletoPayment

| Propriedade  | Tipo            | Descrição                                          |
| ------------ | --------------- | -------------------------------------------------- |
| `barcodeUrl` | String          | Link do código de barras                           |
| `boletoUrl`  | String          | Link da Boleto                                     |
| `dueAt`      | ISO 8601 String | Data de Vencimento do Boleto                       |
| `line`       | String          | Linha digitável do código de barras                |
| `pdfUrl`     | String          | Link do PDF do Boleto                              |
| `qrcodeUrl`  | String          | Link da imagem do QR Code para pagamento do Boleto |

### Shipping

| Propriedade       | Tipo   | Descrição                      |
| ----------------- | ------ | ------------------------------ |
| `shippingCode`    | String | Código de rastreio             |
| `shippingService` | String | Empresa responsável pelo envio |

### Customer

| Propriedade | Tipo    | Descrição                    |
| ----------- | ------- | ---------------------------- |
| `name`      | String  | Nome do Cliente              |
| `email`     | String  | Email do Cliente             |
| `type`      | String  | Tipo descritivo do documento |
| `document`  | String  | Documento do Cliente         |
| `address`   | Address | Endereço do Cliente          |
| `phone`     | Phone   | Contato do Cliente           |

### Address

Quando ativado pelo evento **'abandoned-checkout'** as propriedades de `Address` são opcionais, e podem retornar como strings vazias. Em alguns casos onde o endereço não é informado, `Address` retornará **nulo**.

| Propriedade | Tipo   | Descrição           |
| ----------- | ------ | ------------------- |
| `city`      | String | Cidade              |
| `state`     | String | UF do Estado        |
| `country`   | String | País                |
| `zipCode`   | String | Código Postal (CEP) |
| `line_1`    | String | Logradouro          |
| `line_2`    | String | Complemento         |

### Phone

| Propriedade   | Tipo   | Descrição          |
| ------------- | ------ | ------------------ |
| `countryCode` | String | Código do País     |
| `number`      | String | Número de telefone |
| `areaCode`    | String | Código de área     |
