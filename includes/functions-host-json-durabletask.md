---
title: ta med fil
description: ta med fil
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117072"
---
Konfigurationsinställningar för [varaktiga funktioner](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Hållbara funktioner 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Hållbara funktioner 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Aktivitetsnavnamn måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inget anges är standardaktivitetsnavets namn för en funktionsapp **DurableFunctionsHub**. Mer information finns i [Aktivitetshubbar](../articles/azure-functions/durable-functions-task-hubs.md).

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|hubName (hubName)|Hållbar Funktionshub|Alternativa [aktivitetsnavnamn](../articles/azure-functions/durable-functions-task-hubs.md) kan användas för att isolera flera varaktiga funktioner-program från varandra, även om de använder samma lagringstabnamn.|
|kontrollQueueBatchSize|32|Antalet meddelanden som ska hämtas från kontrollkön åt gången.|
|kontrollQueueBufferThreshold|256|Antalet kontrollkömeddelanden som kan buffras i minnet åt gången, då avsändaren väntar innan du utesluter ytterligare meddelanden.|
|partitionCount |4|Partitionen räknas för kontrollkön. Kan vara ett positivt heltal mellan 1 och 16.|
|controlQueueVisibilityTimeout |5 minuter|Tidsgränsen för synlighet för avstämda kontrollkömeddelanden.|
|workItemQueueVisibilityTimeout |5 minuter|Tidsgränsen för synlighet för kömeddelanden för oställda arbetsobjekt.|
|maxConcurrentActivityFunctions |10X antalet processorer på den aktuella maskinen|Det maximala antalet aktivitetsfunktioner som kan bearbetas samtidigt på en enda värdinstans.|
|maxConcurrentOrchestratorFunktioner |10X antalet processorer på den aktuella maskinen|Det maximala antalet orchestrator-funktioner som kan bearbetas samtidigt på en enda värdinstans.|
|maxQueuePollingInterval|30 sekunder|Det maximala kontroll- och arbetsobjektköavsökningsintervallet i *formatet hh:mm:ss.* Högre värden kan resultera i högre meddelandebehandlingsdämpningsdysligheter. Lägre värden kan resultera i högre lagringskostnader på grund av ökade lagringstransaktioner.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Namnet på appinställningen som har Azure Storage-anslutningssträngen som används för att hantera underliggande Azure Storage-resurser.|
|spåraStoreConnectionStringName||Namnet på en anslutningssträng som ska användas för tabellerna Historik och instanser. Om inget anges `azureStorageConnectionStringName` används anslutningen.|
|spåraStoreNamePrefix||Prefixet som ska användas för tabellerna Historik och instanser när `trackingStoreConnectionStringName` anges. Om inte inställt kommer standardprefixvärdet att vara `DurableTask`. Om `trackingStoreConnectionStringName` det inte anges kommer tabellerna Historik `hubName` och instanser att använda värdet `trackingStoreNamePrefix` som prefix och alla inställningar för ignoreras.|
|traceInputsAndOutputs |false|Ett värde som anger om indata och utdata för funktionsanrop ska spåras. Standardbeteendet vid spårning av funktionskörningshändelser är att inkludera antalet byte i serialiserade indata och utdata för funktionsanrop. Detta ger minimal information om hur indata och utdata ser ut utan uppblåsthet loggarna eller oavsiktligt exponera känslig information. Om du ställer in den här egenskapen på true kan standardfunktionsloggningen logga hela innehållet i funktionsindata och utdata.|
|logReplayEvents|false|Ett värde som anger om händelser för orchestration-repris ska skrivas upp i Application Insights.|
|händelseGridTopicEndpoint ||Url:en för en anpassad ämnesslutpunkt för Azure Event Grid. När den här egenskapen har angetts publiceras meddelanden om orchestration-livscykelmeddelanden till den här slutpunkten. Den här egenskapen stöder lösning av appinställningar.|
|händelseGridKeySettingName ||Namnet på appinställningen som innehåller nyckeln som används för att `EventGridTopicEndpoint`autentisera med det anpassade avsnittet Azure Event Grid på .|
|eventGridPublishRetryCount|0|Antalet gånger som ska försökas om publiceringen i ämnet Händelserutnät misslyckas.|
|händelseGridPublishRetryInterval|5 minuter|Händelserutnätet publicerar återförsöksintervallet i *formatet hh:mm:ss.*|
|eventGridPublishEventTypes||En lista över händelsetyper som ska publiceras i Event Grid. Om inget anges publiceras alla händelsetyper. Tillåtna `Started`värden `Completed` `Failed`inkluderar `Terminated`, , , .|
|användaGracefulShutdown|false|(Förhandsgranska) Aktivera graciöst avstängning för att minska risken för att värdavstängningar misslyckas i processens funktionskörningar.|

Många av dessa inställningar är för att optimera prestanda. Mer information finns i [Prestanda och skala](../articles/azure-functions/durable-functions-perf-and-scale.md).
