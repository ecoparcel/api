### Table of Contents  
[GET Token](#get-token)  
[POST Order](#post-order)  
[GET Tracking](#get-tracking)  

### GET Token
Please contact Integrations@ecoparcel.eu

### POST Order
**Endpoint** `/api/order`

|Headers|||
|-|-|-|
|`Authorization`| Token `XXXXXXXXXX` | Insert API Token |

|Base|||
|-|-|-|
|`orders`| `OrderBase` | Array of Orders to be processed |
|`confirm_and_pay`| *boolean* | Only save or process immediately |

|OrderBase|||
|-|-|-|
|`shipper_details`| `AddressBase` |  *(mandatory)* |
|`consignee_details`| `AddressBase` | *(mandatory)* |
|`parcels`| `ParcelBase` | *(mandatory)* |
|`collection_date`| *dateTime* 
|`customer_reference`| *string* | 


|AddressBase|||
|-|-|-|
|`country`| *string* | *(mandatory)* |
|`company`| *string* | |
|`contact_name`| *string* | *(mandatory)* |
|`address`| *string* | *(mandatory)* |
|`city`| *string* | *(mandatory)* |
|`postcode`| *string* | *(mandatory)* |
|`phone`| *string* | *(mandatory)* |
|`email_address`| *string* | *(mandatory)* |


|ParcelBase|||
|-|-|-|
|`weight`| *int* | *KG* *(mandatory)* |
|`height`| *int* | *CM* *(mandatory)* |
|`width`| *int* | *CM* *(mandatory)* |
|`length`| *int* | *CM* *(mandatory)* |
|`description`| *string* | *(mandatory)* |
|`value`| *float* | *EUR* *(mandatory)* |


#### Example
##### Request
```json
{
    "orders": [
        {
            "shipper_details": {
                "country": "DE",
                "company": "Company GmbH",
                "contact_name": "John",
                "address": "Main Street 1 Apt. 2",
                "email_address": "test@email.de",
                "phone": "+490000000",
                "postcode": "08001",
                "city": "BERLIN"
            },
            "consignee_details": {
                "country": "ES",
                "company": "Company Srl",
                "contact_name": "Jose",
                "email_address": "test@email.es",
                "phone": "+340000000",
                "city": "Madrid",
                "postcode": "28016",
                "address": "Calle de Costa Rica 79"
            },
            "parcels": [
                {
                    "weight": 1,
                    "height": 10,
                    "width": 10,
                    "length": 10,
                    "description": "test cargo",
                    "value": 40
                },
                {
                    "weight": 10,
                    "height": 10,
                    "width": 10,
                    "length": 10,
                    "description": "test",
                    "value": 40
                }
            ],
            "collection_date": "2024-07-31T14:00:00Z",
            "customer_reference": "SKU_001"
        },
        "confirm_and_pay": true
    ]
}
```

##### Response
`labels` returns labels of all orders combined into one PDF file

###### Success 201 - Sufficient Funds to confirm_and_pay
If `confirm_and_pay` was set as True and `paid` is True, that means that Order is confirmed and paid for.
In a result, labels were generated and returned in the response.
```json
{
    "orders": [
        {
            "id": "XXXXX",
            "customer_reference": "XXXXX",
            "parcels": [
                {
                    "id": "XXXXX01"
                },
                {
                    "id": "XXXXX02"
                }
            ],
            "collection_date": "2024-08-01T16:00:00+02:00",
            "total_price": "40.92",
            "paid": true
        }
    ],
    "labels": "BASE64 PDF"
}
```
###### Success 201 - Insufficient Funds to confirm_and_pay
If `confirm_and_pay` was set as `True` and the response `paid` is `False`, that means that the Order was saved, but couldn't be paid for. 
Saved unpaid orders can be revised and paid for at the "My Orders" section at ecoparcel.eu.
```json
{
    "orders": [
        {
            "id": "XXXXX",
            "customer_reference": "XXXXX",
            "parcels": [
                {
                    "id": "XXXXX01"
                },
                {
                    "id": "XXXXX02"
                }
            ],
            "collection_date": "2024-08-01T16:00:00+02:00",
            "total_price": "40.92",
            "paid": false
        }
    ],
    "labels": "BASE64 PDF"
}
```

###### Bad Request 400
```json
{
    "orders": [
        {
            "consignee_details": {
                "city": [
                    "This field is required."
                ],
                "phone": [
                    "This field is required."
                ],
                "email_address": [
                    "This field is required."
                ]
            }
        }
    ]
}
```

### GET Tracking
