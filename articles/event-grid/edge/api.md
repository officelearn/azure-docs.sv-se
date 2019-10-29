---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: REST API på Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ee2b3a35b6f1817b89541a31d0bde4adf00ade2a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992540"
---
# <a name="rest-api"></a>REST-API
I den här artikeln beskrivs REST-API: er för Azure Event Grid på IoT Edge

## <a name="common-api-behavior"></a>Gemensamt API-beteende

### <a name="base-url"></a>Bas-URL
Event Grid på IoT Edge har följande API: er exponerade över HTTP (port 5888) och HTTPS (port 4438).

* Bas-URL för HTTP: http://eventgridmodule:5888
* Bas-URL för HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Frågesträng för begäran
Alla API-begäranden kräver följande frågesträngparametern:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Begär innehålls typ
Alla API-begäranden måste ha en **innehålls typ**.

I händelse av **EventGridSchema** eller **CustomSchema**kan värdet för Content-Type vara något av följande värden:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Om det är **CloudEventSchemaV1_0** i strukturerat läge kan värdet för Content-Type vara något av följande värden:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

I händelse av **CloudEventSchemaV1_0** i binärt läge finns mer information i [dokumentationen](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) .

### <a name="error-response"></a>Fel svar
Alla API: er returnerar ett fel med följande nytto last:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Hantera ämnen

### <a name="put-topic-create--update"></a>Lägg till ämne (Skapa/uppdatera)

**Begäran**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Nytto Last**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Svar**: http 200

**Nytto Last**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Hämta ämne

**Begäran**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Hämta alla ämnen

**Begäran**: ``` GET /topics?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Ta bort ämne

**Begäran**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar**: http 200, Tom nytto Last

## <a name="manage-event-subscriptions"></a>Hantera händelse prenumerationer
I exemplen i det här avsnittet används `EndpointType=Webhook;`. JSON-exemplen för `EndpointType=EdgeHub / EndpointType=EventGrid` finns i nästa avsnitt. 

### <a name="put-event-subscription-create--update"></a>Lägg till händelse prenumeration (Skapa/uppdatera)

**Begäran**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Nytto Last**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Svar**: http 200

**Nytto Last**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Hämta händelse prenumeration

**Begäran**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Hämta händelse prenumerationer

**Begäran**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Svar**: http 200

**Nytto Last**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Ta bort händelse prenumeration

**Begäran**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar**: http 200, ingen nytto Last


## <a name="publish-events-api"></a>API för publicerings händelser

### <a name="send-batch-of-events-in-event-grid-schema"></a>Skicka batch med händelser (i Event Grid schema)

**Begäran**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Svar**: http 200, Tom nytto Last


**Fält beskrivningar för nytto Last**
- ```Id``` är obligatoriskt. Det kan vara valfritt sträng värde som anroparen har fyllt i. Event Grid utför ingen dubblettidentifiering eller använder inga semantik i det här fältet.
- ```Topic``` är valfritt, men om det anges måste det matcha topic_name från begärd URL
- ```Subject``` är obligatorisk, kan vara valfritt sträng värde
- ```EventType``` är obligatorisk, kan vara valfritt sträng värde
- ```EventTime``` är obligatoriskt, verifieras det inte, utan bör vara en korrekt DateTime.
- ```DataVersion``` är obligatoriskt
- ```MetadataVersion``` är valfritt, om det anges måste det vara en sträng med värdet ```"1"```
- ```Data``` är valfritt och kan vara valfri JSON-token (Number, String, Boolean, array, Object)

### <a name="send-batch-of-events-in-custom-schema"></a>Skicka batch med händelser (i anpassat schema)

**Begäran**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Svar**: http 200, Tom nytto Last


**Nytto Last begränsningar**
- MÅSTE vara en matris med händelser.
- Varje mat ris post måste vara ett JSON-objekt.
- Inga andra begränsningar (andra än nytto Last storleken).

## <a name="examples"></a>Exempel

### <a name="set-up-topic-with-eventgrid-schema"></a>Konfigurera ämne med EventGrid-schema
Ställer in ett ämne för att kräva att händelser publiceras i **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Konfigurera ämne med anpassat schema
Ställer in ett ämne för att kräva att händelser publiceras i `customschema`.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Konfigurera ämne med moln händelse schema
Ställer in ett ämne för att kräva att händelser publiceras i `cloudeventschema`.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Konfigurera webhook som mål, händelser som ska levereras i eventgridschema
Använd den här måltypen för att skicka händelser till andra moduler (som är värdar för en HTTP-slutpunkt) eller till valfri HTTP-adresserad slut punkt i nätverket/Internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Begränsningar för attributet `endpointUrl`:
- Det får inte vara null.
- Det måste vara en absolut URL.
- Om outbound__webhook__httpsOnly är inställt på sant i EventGridModule-inställningarna måste det endast vara HTTPS.
- Om outbound__webhook__httpsOnly är inställt på false kan det vara HTTP eller HTTPS.

Begränsningar för egenskapen `eventDeliverySchema`:
- Den måste överensstämma med det underbeskrivande ämnets schema.
- Det kan vara null. Det används som standard för det här avsnittets schema.

### <a name="set-up-iot-edge-as-destination"></a>Konfigurera IoT Edge som mål

Använd det här målet för att skicka händelser till IoT Edge hubb och omfattas av del systemet routning/filtrering/vidarebefordran för Edge Hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Konfigurera Event Grid molnet som mål

Använd det här målet för att skicka händelser till Event Grid i molnet (Azure). Du måste först konfigurera ett användar ämne i molnet som händelser ska skickas till innan du skapar en händelse prenumeration på gränsen.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Det får inte vara null.
- Det måste vara en absolut URL.
- Sökvägen `/api/events` måste definieras i URL-sökvägen för begäran.
- Den måste ha `api-version=2018-01-01` i frågesträngen.
- Om outbound__eventgrid__httpsOnly är inställt på sant i EventGridModule-inställningarna (sant som standard), måste det endast vara HTTPS.
- Om outbound__eventgrid__httpsOnly har angetts till false kan det vara HTTP eller HTTPS.
- Om outbound__eventgrid__allowInvalidHostnames är inställt på falskt (falskt som standard) måste den ha en av följande slut punkter:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Får inte vara null.

TopicName:
- Om Subscription. EventDeliverySchema är inställt på EventGridSchema placeras värdet från det här fältet i varje händelses ämnes fält innan det vidarebefordras till Event Grid i molnet.
- Om Subscription. EventDeliverySchema har angetts till CustomEventSchema ignoreras den här egenskapen och den anpassade händelse nytto lasten vidarebefordras exakt som den togs emot.
