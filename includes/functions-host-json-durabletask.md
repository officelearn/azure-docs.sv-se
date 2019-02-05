---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a3af711503445000d9613feb2eec7967442fe538
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735956"
---
Konfigurationsinställningar för [varaktiga funktioner](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges är standardnamnet på uppgiften hub för en funktionsapp **DurableFunctionsHub**. Mer information finns i [uppgift hubs](../articles/azure-functions/durable-functions-task-hubs.md).

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternativa [uppgift hub](../articles/azure-functions/durable-functions-task-hubs.md) namn kan användas för att isolera program för flera varaktiga funktioner från varandra, även om de använder samma lagringserverdel.|
|ControlQueueBatchSize|32|Antal meddelanden att hämta från kön kontroll i taget.|
|PartitionCount |4|Antalet partitioner för kön kontroll. Kan vara ett positivt heltal mellan 1 och 16.|
|ControlQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön kontroll Kömeddelanden.|
|WorkItemQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön work item Kömeddelanden.|
|MaxConcurrentActivityFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet Aktivitetsfunktioner som kan bearbetas samtidigt på en enda värd-instans.|
|MaxConcurrentOrchestratorFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet orchestrator-funktioner som kan bearbetas samtidigt på en enda värd-instans.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Namnet på den appinställning som har Azure Storage-anslutningssträng som används för att hantera de underliggande Azure-lagringsresurserna.|
|TraceInputsAndOutputs |false|Ett värde som anger om du vill spåra indata och utdata av funktionsanrop. Standardbeteende när spårning av händelser för körning av funktionen är att inkludera antalet byte i serialiserade indata och utdata för funktionsanrop. Detta ger minimalt med information om hur indata och utdata ut utan svullen buk loggarna eller av misstag exponera känslig information till loggarna. Denna egenskap anges till true kommer funktionen Standardloggning logga hela innehållet i funktionen indata och utdata.|
|LogReplayEvents|false|Ett värde som anger om du vill skriva orchestration repetitionsattacker händelser till Application Insights.|
|EventGridTopicEndpoint ||URL till en slutpunkt för Azure Event Grid-anpassat ämne. När den här egenskapen anges publiceras orchestration livscykel meddelandehändelser till den här slutpunkten. Den här egenskapen stöder Appinställningar skärmupplösning.|
|EventGridKeySettingName ||Namnet på den appinställning som innehåller den nyckel som används för att autentisera med det anpassade ämnet Azure Event Grid på `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Det går inte att antalet gånger att försöka igen om publicering till Event Grid-ämne.|
|EventGridPublishRetryInterval|5 minuter|Event Grid publicerar återförsöksintervallet i den *: mm: ss* format.|

Många av dessa är för att optimera prestanda. Mer information finns i [prestanda och skalning](../articles/azure-functions/durable-functions-perf-and-scale.md).