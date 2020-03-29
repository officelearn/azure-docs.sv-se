---
title: REST API - Azure Event Grid IoT Edge | Microsoft-dokument
description: REST API på Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841837"
---
# <a name="rest-api"></a>REST API
I den här artikeln beskrivs REST-API:erna för Azure Event Grid på IoT Edge

## <a name="common-api-behavior"></a>Gemensamt API-beteende

### <a name="base-url"></a>Grundläggande URL
Event Grid på IoT Edge har följande API:er exponeras över HTTP (port 5888) och HTTPS (port 4438).

* Bas-URL för HTTP:http://eventgridmodule:5888
* Grundläggande URL för HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Begär frågesträng
Alla API-begäranden kräver följande frågesträngparameter:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Ange innehållstyp för begäran
Alla API-begäranden måste ha en **content-type**.

Vid **EventGridSchema** eller **CustomSchema**kan värdet för Content-Type vara ett av följande värden:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Vid **CloudEventSchemaV1_0** i strukturerat läge kan värdet för content-type vara ett av följande värden:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Om **CloudEventSchemaV1_0** i binärt läge finns i [dokumentationen](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) för mer information.

### <a name="error-response"></a>Felsvar
Alla API:er returnerar ett fel med följande nyttolast:

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

### <a name="put-topic-create--update"></a>Sätt ämne (skapa / uppdatera)

**Begäran**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Nyttolast:**

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Svar**: HTTP 200

**Nyttolast:**

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

### <a name="get-topic"></a>Få ämne

**Begäran**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar**: HTTP 200

**Nyttolast:**
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

**Begäran**:``` GET /topics?api-version=2019-01-01-preview ```

**Svar**: HTTP 200

**Nyttolast:**
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

**Begäran**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Svar:** HTTP 200, tom nyttolast

## <a name="manage-event-subscriptions"></a>Hantera händelseprenumerationer
Exempel i det `EndpointType=Webhook;`här avsnittet använder . Json-proverna `EndpointType=EdgeHub / EndpointType=EventGrid` för finns i nästa avsnitt. 

### <a name="put-event-subscription-create--update"></a>Placera händelseprenumeration (skapa/uppdatera)

**Begäran**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Nyttolast:**
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
        "persistencePolicy": "true",
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

**Svar**: HTTP 200

**Nyttolast:**

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


### <a name="get-event-subscription"></a>Hämta evenemangsprenumeration

**Begäran**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar**: HTTP 200

**Nyttolast:**
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

### <a name="get-event-subscriptions"></a>Hämta evenemangsprenumerationer

**Begäran**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Svar**: HTTP 200

**Nyttolast:**
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

### <a name="delete-event-subscription"></a>Ta bort händelseprenumeration

**Begäran**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Svar:** HTTP 200, ingen nyttolast


## <a name="publish-events-api"></a>API för publicering av händelser

### <a name="send-batch-of-events-in-event-grid-schema"></a>Skicka grupp av händelser (i schema för händelserutnät)

**Begäran**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

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

**Svar:** HTTP 200, tom nyttolast


**Beskrivningar av nyttolastfält**
- ```Id```är obligatoriskt. Det kan vara vilket strängvärde som helst som befolkas av anroparen. Händelserutnät gör INTE någon dubblettidentifiering eller framtvingande av semantik i det här fältet.
- ```Topic```är valfritt, men om det anges måste matcha topic_name från begäran URL
- ```Subject```är obligatoriskt, kan vara vilket strängvärde som helst
- ```EventType```är obligatoriskt, kan vara vilket strängvärde som helst
- ```EventTime```är obligatoriskt, det är inte validerat men bör vara en riktig DateTime.
- ```DataVersion```är obligatoriskt
- ```MetadataVersion```är valfritt, om det anges måste det vara en sträng med värdet```"1"```
- ```Data```är valfritt och kan vara vilken JSON-token som helst (tal, sträng, boolesk, matris, objekt)

### <a name="send-batch-of-events-in-custom-schema"></a>Skicka grupp av händelser (i anpassat schema)

**Begäran**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Svar:** HTTP 200, tom nyttolast


**Begränsningar för nyttolast**
- MÅSTE vara en rad händelser.
- Varje matrispost MÅSTE vara ett JSON-objekt.
- Inga andra begränsningar (förutom nyttolaststorlek).

## <a name="examples"></a>Exempel

### <a name="set-up-topic-with-eventgrid-schema"></a>Konfigurera ämne med EventGrid-schema
Ställer in ett ämne som kräver att händelser publiceras i **eventgridschema**.

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
Skapar ett ämne som kräver att `customschema`händelser publiceras i .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Konfigurera ämne med molnhändelseschema
Skapar ett ämne som kräver att `cloudeventschema`händelser publiceras i .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Konfigurera WebHook som mål, händelser som ska levereras i eventgridschema
Använd den här måltypen om du vill skicka händelser till en annan modul (som är värd för en HTTP-slutpunkt) eller till en http-adresserbar slutpunkt i nätverket/internet.

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

Begränsningar för `endpointUrl` attributet:
- Det måste vara icke-null.
- Det måste vara en absolut webbadress.
- Om outbound__webhook__httpsOnly är inställt på true i EventGridModule-inställningarna måste den bara vara HTTPS.
- Om outbound__webhook__httpsOnly är inställd på false kan det vara HTTP eller HTTPS.

Begränsningar för `eventDeliverySchema` fastigheten:
- Det måste matcha indataschemat för prenumereran.
- Det kan vara null. Ämnets indataschema är som standard.

### <a name="set-up-iot-edge-as-destination"></a>Konfigurera IoT Edge som mål

Använd det här målet om du vill skicka händelser till IoT Edge Hub och utsättas för edge-hubbens undersystem för routning/filtrering/vidarebefordran.

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

### <a name="set-up-event-grid-cloud-as-destination"></a>Konfigurera Event Grid Cloud som mål

Använd den här målet för att skicka händelser till Event Grid i molnet (Azure). Du måste först konfigurera ett användarämne i molnet som händelser ska skickas till innan du skapar en händelseprenumeration på gränsen.

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

SlutpunktUrl:
- Det måste vara icke-null.
- Det måste vara en absolut webbadress.
- Sökvägen `/api/events` måste definieras i url-sökvägen för begäran.
- Det måste `api-version=2018-01-01` finnas i frågesträngen.
- Om outbound__eventgrid__httpsOnly är inställt på true i EventGridModule-inställningarna (sant som standard) måste den bara vara HTTPS.
- Om outbound__eventgrid__httpsOnly är inställt på false kan det vara HTTP eller HTTPS.
- Om outbound__eventgrid__allowInvalidHostnames är inställt på false (falskt som standard) måste den inriktas på en av följande slutpunkter:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Måste vara icke-null.

TopicName:
- Om Subscription.EventDeliverySchema är inställt på EventGridSchema anges värdet från det här fältet i varje händelse ämnesfält innan det vidarebefordras till Event Grid i molnet.
- Om Subscription.EventDeliverySchema är inställt på CustomEventSchema ignoreras den här egenskapen och den anpassade händelsenyttolasten vidarebefordras precis som den togs emot.

## <a name="set-up-event-hubs-as-a-destination"></a>Konfigurera händelsehubbar som mål

Om du vill publicera till `endpointType` `eventHub` en eventhubb ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för den specifika händelsehubben som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Du kan generera en entitetsspecifik anslutningssträng genom att navigera till den specifika händelsehubb som du vill publicera till i Azure Portal och klicka på principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Ställa in servicebussköer som mål

Om du vill publicera till `endpointType` en `serviceBusQueue` servicebusskö ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för den specifika servicebusskö som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Generera en entitetsspecifik anslutningssträng genom att navigera till den specifika servicebusskö som du vill publicera till i Azure Portal och klicka på Principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Ställa in servicebussavsnitt som mål

Om du vill publicera till `endpointType` ett `serviceBusTopic` servicebussavsnitt ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för det specifika servicebussämne som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Generera en entitetsspecifik anslutningssträng genom att navigera till det specifika servicebussämne som du vill publicera till i Azure Portal och klicka på principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Ställa in lagringsköer som mål

Om du vill publicera i `endpointType` `storageQueue` en lagringskö ställer du in och tillhandahåller:

* queueName: Namnet på den lagringskö som du publicerar till.
* connectionString: Anslutningssträng för lagringskontot som lagringskön finns i.

    >[!NOTE]
    > Avgränsade händelsehubbar, servicebussköer och servicebussavsnitt, anslutningssträngen som används för lagringsköer är inte entitetsspecifik. I stället måste den bara anslutningssträngen för lagringskontot.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```