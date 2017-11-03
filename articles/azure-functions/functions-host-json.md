---
title: "Host.JSON referens för Azure Functions"
description: "I referensdokumentationen för Azure Functions host.json filen."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: b3e5976a84e0ec91a41d683a426b58635fd5abd6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="hostjson-reference-for-azure-functions"></a>Host.JSON referens för Azure Functions

Den *host.json* metadatafil innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. Den här artikeln innehåller de inställningar som är tillgängliga. JSON-schema är på http://json.schemastore.org/host.

Det finns andra globala konfigurationsalternativ i [appinställningar](functions-app-settings.md) och i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="sample-hostjson-file"></a>Exempelfilen host.json

I följande exempel *host.json* filen har alla angivna möjliga alternativ.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

I följande avsnitt i den här artikeln beskrivs varje översta egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>Aggregator

Anger hur många funktionsanrop sammanställs när [beräkna mått för Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Högsta antal begäranden ska aggregeras.| 
|flushTimeout|00:00:30|Maximal tid period ska aggregeras.| 

Funktionsanrop aggregeras när först av två begränsar har uppnåtts.

## <a name="applicationinsights"></a>applicationInsights

Kontroller av [provtagning funktion i Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|IsEnabled|FALSKT|Aktiverar eller inaktiverar provtagning.| 
|maxTelemetryItemsPerSecond|5|Tröskelvärdet på vilka provtagning börjar.| 

## <a name="eventhub"></a>EventHub

Konfigurationsinställning för [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md).

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Den maximala händelseantal tas emot varje receive-loop.|
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande EventProcessorHost.| 
|batchCheckpointFrequency|1|Antal händelsebatchar med att bearbeta innan du skapar en kontrollpunkt för EventHub-markör.| 

## <a name="functions"></a>Funktioner

En lista över funktioner som värd för jobbet ska köras.  En tom matris innebär att köra alla funktioner.  Avsedd att användas endast när [körs lokalt](functions-run-local.md). I funktionen appar använda den *function.json* `disabled` egenskapen i stället för den här egenskapen i *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger timeout-varaktighet för alla funktioner. Det giltiga intervallet är mellan 1 sekund till 10 minuter i förbrukning planer, och standardvärdet är 5 minuter. Det finns ingen gräns i App Service-planer, och standardvärdet är null, vilket indikerar ingen tidsgräns.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="http"></a>http

Konfigurationsinställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|routePrefix|api|Prefixet för vägen som gäller för alla vägar. Ta bort prefixet som standard med hjälp av en tom sträng. |
|maxOutstandingRequests|-1|Maximalt antal väntande förfrågningar som hålls kvar vid en given tidpunkt (-1 innebär unbounded). Gränsen inkluderar begäranden som står i kö men har inte startats, samt alla pågående körningar. Alla inkommande förfrågningar via den här gränsen avvisas med ett 429 ”upptagen” svar. Anropare kan använda det svaret för att använda strategier för tidsbaserade försök igen. Kontrollerar den här inställningen bara queuing som sker inom jobbet värden körning av sökväg. Andra köer, till exempel begärandekön ASP.NET påverkas inte av den här inställningen. |
|maxConcurrentRequests|-1|Maximalt antal HTTP-funktioner som körs parallellt (-1 innebär unbounded). Du kan till exempel ange en gräns om HTTP-funktioner använder för mycket systemresurser när samtidighet är hög. Eller om dina funktioner gör utgående förfrågningar till en tjänst från tredje part, anropen behöva vara begränsad hastighet.|
|dynamicThrottlesEnabled|FALSKT|Gör en begäran om bearbetning pipeline att regelbundet kontrollera prestandaräknare för system. Räknare som inkluderar anslutningar, trådar, processer, minne och cpu. Om någon av räknarna som är över en inbyggd tröskelvärdet (80%) avvisas förfrågningar med svaret 429 ”upptagen” förrän counter(s) återgå till normal nivå.|

## <a name="id"></a>id

Unikt ID för en värd för jobbet. Vara kan en gemen GUID med bindestreck bort. Krävs när du kör programmet lokalt. När den körs i Azure Functions ID skapas automatiskt om `id` utelämnas.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Loggaren

Kontroller filtrering för loggar som skrivits av en [ILogger objekt](functions-monitoring.md#write-logs-in-c-functions) eller [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|categoryFilter|Saknas|Anger att filtrera efter kategori| 
|defaultLevel|Information|Alla kategorier som angetts i den `categoryLevels` kan skicka loggar på denna nivå och senare till Application Insights.| 
|categoryLevels|Saknas|En matris med kategorier som anger den minsta loggnivån som skickas till Application Insights för varje kategori. Den kategori som anges här styr alla kategorier som börjar med samma värde, och längre värden företräde. I föregående exempel *host.json* -fil, alla kategorier som börjar med ”Host.Aggregator”-loggen på `Information` nivå. Alla kategorier som börjar med ”värd”, till exempel ”Host.Executor” logga in på `Error` nivå.| 

## <a name="queues"></a>Köer

Konfigurationsinställningar för [lagring-utlösare och bindningar](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximalt intervall i millisekunder mellan kön avsöker.| 
|visibilityTimeout|0|Det går inte att tidsintervallet mellan nya försök vid bearbetning av ett meddelande.| 
|batchSize|16|Antal meddelanden i kö att hämta och bearbeta parallellt. Det högsta antalet är 32.| 
|maxDequeueCount|5|Antal gånger för bearbetning av ett meddelande innan du flyttar till skadligt kön.| 
|newBatchThreshold|batchSize-2|Tröskelvärdet som en ny grupp med meddelanden hämtas.| 

## <a name="servicebus"></a>ServiceBus

Konfigurationsinställning för [Service Bus-utlösare och bindningar](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maximalt antal samtidiga anrop till vilket motanrop som meddelandet pump ska starta. | 
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximal varaktighet inom vilken meddelandet låset förnyas automatiskt.| 

## <a name="singleton"></a>Singleton

Konfigurationsinställningar för Singleton låsa beteende. Mer information finns i [GitHub problemet om singleton-stöd](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktionen nivån Lås används för. Lås förnyelse automatisk.| 
|listenerLockPeriod|00:01:00|Den period som lyssnare Lås används för.| 
|listenerLockRecoveryPollingInterval|00:01:00|Tidsintervall som används för lyssnare låsåterställning om inte det gick att hämta en lyssnare Lås vid start.| 
|lockAcquisitionTimeout|00:01:00|Längsta tid körningsmiljön kommer att försöka skaffa ett lås.| 
|lockAcquisitionPollingInterval|Saknas|Intervall mellan försök för anskaffning av Lås.| 

## <a name="tracing"></a>spårning

Konfigurationsinställningar för loggarna som du skapar med hjälp av en `TraceWriter` objekt. Se [C# loggning](functions-reference-csharp.md#logging) och [Node.js loggning](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|consoleLevel|Info|Spårning-nivå för konsolen loggning. Alternativen är: `off`, `error`, `warning`, `info`, och `verbose`.|
|fileLoggingMode|debugOnly|Spårning-nivå för filen loggning. Alternativen är `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

En uppsättning [delade kod kataloger](functions-reference-csharp.md#watched-directories) som bör övervakas för ändringar.  Garanterar att när koden i de här katalogerna ändras ändringarna fångas upp av dina funktioner.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

[Uppgiften hubb](durable-functions-task-hubs.md) för [varaktiga funktioner](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Hubb aktivitetsnamn måste börja med en bokstav och endast bestå av bokstäver och siffror. Om inget anges är standardnamnet för aktiviteten hubb för en funktionsapp **DurableFunctionsHub**. Mer information finns i [uppgift hubbar](durable-functions-task-hubs.md).


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du uppdaterar filen host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Globala inställningar i miljövariabler](functions-app-settings.md)
