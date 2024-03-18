# API de Vendas

Aqui você encontrará uma visão geral da API de vendas e como integrá-la à sua aplicação.

## Índice

- [Autenticação](#autenticação)
  - [Como Gerar o Token de Acesso](#como-gerar-o-token-de-acesso)
- [Busca de Vendas](#busca-de-vendas-get-integrationsales)
  - [Query Params](#query-params-vendas)
  - [Exemplo de Resposta](#exemplo-resposta-vendas)
  - [Tipos da Resposta](#tipos-da-resposta-vendas)
    - [Sale](#sale)
    - [Customer](#customer)
    - [Product](#product)
- [Busca de Produtos](#busca-de-produtos-get-integrationproducts)
  - [Query Params](#query-params-produtos)
  - [Exemplo de Resposta](#exemplo-de-resposta-produtos)
  - [Tipos da Resposta](#tipos-da-resposta-produtos)
- [Alterar Código de Rastreio](#alterar-código-de-rastreio-patch-integrationsale-shipping)
  - [Payload](#payload-rastreio)
  - [Resposta](#resposta-rastreio)

## Autenticação

A autenticação de todas as rotas é feita pelo header `Authorization`, utilizando Bearer Token.
Para utilizar a rota é necessário gerar um token de acesso do usuário.

### Como Gerar o Token de Acesso

Para gerar o token é simples, você precisará somente de uma conta completa na The Mart.

1. Siga para o menu API sob o sub-menu Ferramentas, no menu lateral.
2. Clique no botão (+) no canto superior direito.
3. De um apelido para facilitar a identificação do novo token e clique em Salvar.
4. Pronto, seu token foi gerado, e você pode copiá-lo utilizando o botão com icone :link: na lateral direita da tabela.

## Busca de Vendas (`GET /integration/sales`)

Utilize esta rota para listar as vendas do usuário autenticado, aplicando filtros de status, data, paginação e ordernação.

```bash
curl --request GET \
  --url 'https://the-mart-api.herokuapp.com/integration/sales?orderBy=productName&sort=asc' \
  --header 'Accept: application/json, text/plain, */*' \
  --header 'Authorization: Bearer {Bearer Token}' \
```

### Query Params (Vendas)

| Campo       | Valor Exemplo                  | Descrição                                                                               |
| ----------- | ------------------------------ | --------------------------------------------------------------------------------------- |
| `startDate` | `"2024-01-09T00:00:00.000Z"`   | Data padrão ISO 8601                                                                    |
| `endDate`   | `"2024-01-09T23:59:59.999"`    | Data padrão ISO 8601                                                                    |
| `status`    | `"success" \| "pending"`       | Status da Venda                                                                         |
| `orderBy`   | `"createdAt" \| "productName"` | Valor padrão de ordenação é pelo campo `createdAt`                                      |
| `sort`      | `"asc" \| "desc"`              | Valor padrão de ordenação é `asc`                                                       |
| `page`      | `1`                            | Página para Paginação                                                                   |
| `quantity`  | `10`                           | Quantidade por página ao realizar paginação. O valor padrão é 10. O valor máximo é 100. |

### Exemplo Resposta (Vendas)

```JSON
{
   "totalCount":1,
   "results":[
      {
         "sale":{
            "saleId":"or_xxxxxxxx",
            "TotalCheckout":20,
            "createdAt":"2024-01-12T13:38:19.314Z",
            "updatedAt":"2024-01-12T13:38:29.272Z",
            "status":"success",
            "totalProducts":20,
            "totalFrete":0,
            "discount":0,
            "productQty": 1,
            "paymentMethod": "credit_card"
         },
         "customer":{
            "customerId":"cus_xxxxxxx",
            "customerName":"Nome de Teste",
            "customerDocument":"33199554479",
            "customerEmail":"emailde@teste.com",
            "customerPhone":"+5599999999999",
            "address":"Rua Teste Para Entregas",
            "zipcode":"89033259",
            "number":"999",
            "complement":"Complemento 123",
            "neighborhood":"Velha",
            "city":"Blumenau",
            "state":"SC",
            "country":"BR"
         },
         "products":[
            {
               "ProductID":"themartcc1977-ddfe-4180-bcf9-843819970512",
               "productName":"Nome do Produto",
               "productUnitaryValue":20
            }
         ]
      }
   ]
}
```

### Tipos da Resposta (Vendas)

| Propriedades | Tipo      | Descrição                             |
| ------------ | --------- | ------------------------------------- |
| `totalcount` | Number    | Numero total de registros encontrados |
| `results`    | Object[]  | Registros encontrados                 |
| `sale`       | Sale      | Informações da Venda                  |
| `customer`   | Customer  | Informações do Comprador              |
| `products`   | Product[] | Informações dos Produtos da Venda     |

#### Sale

| Propriedades    | Tipo            | Descrição                            |
| --------------- | --------------- | ------------------------------------ |
| `saleId`        | String          | Identificador único da Venda         |
| `TotalCheckout` | Number          | Valor total da Venda                 |
| `totalProducts` | Number          | Valor agregado dos produtos da Venda |
| `status`        | String          | Status da Venda                      |
| `paymentMethod` | PaymentMethod   | Método de Pagamento da Venda         |
| `totalFrete`    | Number          | Valor do frete                       |
| `discount`      | Number          | Valor de Desconto                    |
| `productQty`    | Number          | Quantidade do Produto                |
| `createdAt`     | ISO 8601 String | Data de criação da Venda             |
| `updatedAt`     | ISO 8601 String | Última atualização da Venda          |

PaymentMethod = `'credit_card'` | `'boleto'` | `'pix'`

#### Customer

| Propriedades       | Tipo   | Descrição                        |
| ------------------ | ------ | -------------------------------- |
| `customerId`       | String | Identificador único do Comprador |
| `customerName`     | String | Nome                             |
| `customerDocument` | String | Documento                        |
| `customerEmail`    | String | Email                            |
| `customerPhone`    | String | Telefone                         |
| `address`          | String | Logradouro                       |
| `zipcode`          | String | Código Postal (CEP)              |
| `number`           | String | Número da Casa                   |
| `complement`       | String | Complemento do Endereço          |
| `neighborhood`     | String | Bairro                           |
| `city`             | String | Cidade                           |
| `state`            | String | Estado                           |
| `country`          | String | País                             |

#### Product

| Propriedades          | Tipo   | Descrição                      |
| --------------------- | ------ | ------------------------------ |
| `ProductID`           | String | Identificador único do Produto |
| `productName`         | String | Nome do Produto                |
| `productUnitaryValue` | Number | Valor unitário                 |

## Busca de Produtos (`GET /integration/products`)

Utilize esta rota para listar os produtos do usuário autenticado, aplicando filtros de paginação e ordernação.

```bash
curl --request GET \
  --url https://the-mart-api.herokuapp.com/integration/products \
  --header 'Accept: application/json, text/plain, */*' \
  --header 'Authorization: Bearer {Bearer Token}'
```

### Query Params (Produtos)

| Campo      | Valor Exemplo           | Descrição                                                                               |
| ---------- | ----------------------- | --------------------------------------------------------------------------------------- |
| `orderBy`  | `"createdAt" \| "name"` | Valor padrão de ordenação é pelo campo `createdAt`                                      |
| `sort`     | `"asc" \| "desc"`       | Valor padrão de ordenação é `asc`                                                       |
| `page`     | `1`                     | Página para Paginação                                                                   |
| `quantity` | `10`                    | Quantidade por página ao realizar paginação. O valor padrão é 10. O valor máximo é 100. |

### Exemplo de Resposta (Produtos)

```JSON
{
   "totalCount":2,
   "results":[
      {
         "ProductID":"xxxxxxxx-ddfe-1997-bcf9-843838530101",
         "productName":"Nome do Produto"
      },
      {
         "ProductID":"xxxxxxxx-ddfe-1997-bcf9-843838530202",
         "productName":"Nome do Produto 2"
      }
   ]
}
```

### Tipos da Resposta (Produtos)

| Propriedades  | Tipo     | Descrição                             |
| ------------- | -------- | ------------------------------------- |
| `totalcount`  | Number   | Numero total de registros encontrados |
| `results`     | Object[] | Registros encontrados                 |
| `ProductID`   | String   | Identificador único do Produto        |
| `productName` | String   | Nome do Produto                       |

## Alterar Código de Rastreio (`PATCH /integration/sale-shipping`)

Utilize esta rota para atualizar ou inserir o código de rastreio e provedor logístico de uma venda.

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

## Payload (Rastreio)

| Propriedades      | Tipo     | Descrição                                             |
| ----------------- | -------- | ----------------------------------------------------- |
| `data`            | Object[] | Vendas a serem atualizadas e seus dados               |
| `saleId`          | String   | Identificador único da venda (`tmor_...` ou `or_...`) |
| `shippingService` | String   | Serviço de Logistica (correios, jadlog, etc)          |
| `shippingCode`    | String   | Código de Rastreio                                    |

## Resposta (Rastreio)

A resposta consiste em arrays de controle dos IDs fornecidos, listando os bem sucedidos, os que causaram falha, e os que não foram encontrados.

| Propriedades        | Tipo     | Descrição                                          |
| ------------------- | -------- | -------------------------------------------------- |
| `successfulUpdates` | String[] | Identificadores das vendas atualizadas com sucesso |
| `failedUpdates`     | String[] | Identificadores das vendas que causaram falha      |
| `notFoundOrders`    | String[] | Identificadores das vendas não encontradas         |
