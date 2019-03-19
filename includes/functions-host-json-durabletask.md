---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58137641"
---
Konfigurationsinställningar för [varaktiga funktioner](../articles/azure-functions/durable-functions-overview.md).

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
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges är standardnamnet på uppgiften hub för en funktionsapp **DurableFunctionsHub**. Mer information finns i [uppgift hubs](../articles/azure-functions/durable-functions-task-hubs.md).

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternativa [uppgift hub](../articles/azure-functions/durable-functions-task-hubs.md) namn kan användas för att isolera program för flera varaktiga funktioner från varandra, även om de använder samma lagringserverdel.|
|controlQueueBatchSize|32|Antal meddelanden att hämta från kön kontroll i taget.|
|partitionCount |4|Antalet partitioner för kön kontroll. Kan vara ett positivt heltal mellan 1 och 16.|
|controlQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön kontroll Kömeddelanden.|
|workItemQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön work item Kömeddelanden.|
|maxConcurrentActivityFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet Aktivitetsfunktioner som kan bearbetas samtidigt på en enda värd-instans.|
|maxConcurrentOrchestratorFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet orchestrator-funktioner som kan bearbetas samtidigt på en enda värd-instans.|
|maxQueuePollingInterval|30 sekunder|Kontroll och arbetsobjektet kö avsökningsintervall i den *: mm: ss* format. Högre värden kan resultera i högre svarstider för meddelandebehandling. Lägre värden kan resultera i högre kostnader för lagring på grund av ökad lagringstransaktioner.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Namnet på den appinställning som har Azure Storage-anslutningssträng som används för att hantera de underliggande Azure-lagringsresurserna.|
|trackingStoreConnectionStringName||Namnet på en anslutningssträng att använda för tabellerna historik och instanser. Om inte anges den `azureStorageConnectionStringName` anslutningen används.|
|trackingStoreNamePrefix||Prefixet som ska användas för historik och instanser tabeller när `trackingStoreConnectionStringName` har angetts. Om inte har angetts prefixvärdet standard blir `DurableTask`. Om `trackingStoreConnectionStringName` inte anges i tabellerna historik och instanser att använda den `hubName` värde som deras prefix och alla andra inställningar för `trackingStoreNamePrefix` kommer att ignoreras.|
|traceInputsAndOutputs |false|Ett värde som anger om du vill spåra indata och utdata av funktionsanrop. Standardbeteende när spårning av händelser för körning av funktionen är att inkludera antalet byte i serialiserade indata och utdata för funktionsanrop. Det här beteendet ger minimalt med information om hur indata och utdata ut utan svullen buk loggarna eller av misstag exponera känslig information. Denna egenskap anges till true kommer funktionen Standardloggning logga hela innehållet i funktionen indata och utdata.|
|logReplayEvents|false|Ett värde som anger om du vill skriva orchestration repetitionsattacker händelser till Application Insights.|
|eventGridTopicEndpoint ||URL till en slutpunkt för Azure Event Grid-anpassat ämne. När den här egenskapen anges publiceras orchestration livscykeln meddelandehändelser till den här slutpunkten. Den här egenskapen stöder Appinställningar skärmupplösning.|
|eventGridKeySettingName ||Namnet på den appinställning som innehåller den nyckel som används för att autentisera med det anpassade ämnet Azure Event Grid på `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Det går inte att antalet gånger att försöka igen om publicering till Event Grid-ämne.|
|eventGridPublishRetryInterval|5 minuter|Event Grid publicerar återförsöksintervallet i den *: mm: ss* format.|
|eventGridPublishEventTypes||En lista över typer av att publicera till Event Grid. Om inte anges kommer att publiceras alla händelsetyper. Tillåtna värden är `Started`, `Completed`, `Failed`, `Terminated`.|

Många av de här inställningarna är för att optimera prestanda. Mer information finns i [prestanda och skalning](../articles/azure-functions/durable-functions-perf-and-scale.md).