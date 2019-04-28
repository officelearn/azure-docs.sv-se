---
title: Host.JSON-referens för Azure Functions 1.x
description: Referensdokumentation för Azure Functions host.json filen med v1-runtime.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107082"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Host.JSON-referens för Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Den *host.json* metadatafilen innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. Den här artikeln innehåller de inställningar som är tillgängliga för v1-runtime. JSON-schemat är i http://json.schemastore.org/host.

> [!NOTE]
> Den här artikeln är för Azure Functions 1.x.  En referens för host.json i Functions 2.x kan se [host.json-referens för Azure Functions 2.x](functions-host-json.md).

Andra konfigurationsalternativ för funktionen app hanteras i din [appinställningar](functions-app-settings.md).

Vissa host.json-inställningarna används bara när du kör lokalt i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="sample-hostjson-file"></a>Exempelfilen för host.json

I följande exempel *host.json* filer har alla möjliga alternativ som har angetts.


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
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
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

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Konfigurationsinställningar för [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

En lista över funktioner som värd för jobb körs. En tom matris innebär att köra alla funktioner. Avsedd att användas endast när [körs lokalt](functions-run-local.md). I funktionsappar i Azure, bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) att inaktivera specifika funktioner i stället för den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger timeout-varaktighet för alla funktioner. Det giltiga intervallet är mellan 1 sekund till 10 minuter i en serverlös förbrukningsplan och standardvärdet är 5 minuter. Det finns ingen övergripande gräns i en App Service-plan och standard beroende av runtime-versionen.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Konfigurationsinställningar för [Övervakare för hälsa för värd](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|aktiverad|true|Anger om funktionen är aktiverad. | 
|healthCheckInterval|10 sekunder|Tidsintervallet mellan regelbunden hälsotillståndet kontrollerar. | 
|healthCheckWindow|2 minuter|En glidande tidsperiod som används tillsammans med den `healthCheckThreshold` inställningen.| 
|healthCheckThreshold|6|Hur många gånger hälsokontrollen kan misslyckas innan återvinning värd har initierats.| 
|counterThreshold|0.80|Tröskelvärdet som en prestandaräknare anses vara felaktiga.| 

## <a name="http"></a>http

Konfigurationsinställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Version 1.x endast.*

Unikt ID för en värd för jobbet. Kan vara en gemen GUID med bindestreck tas bort. Krävs när du kör lokalt. När du kör i Azure rekommenderar vi att du inte anger ett ID-värde. Ett ID genereras automatiskt i Azure när `id` utelämnas. 

Om du delar ett Storage-konto i flera funktionsappar, se till att varje funktionsapp har en annan `id`. Du kan utelämna den `id` egenskapen eller manuellt ange varje funktionsapp `id` på ett annat värde. Timer utlösaren använder ett storage-Lås för att se till att endast en timer-instans när en funktionsapp skalas ut till flera instanser. Om två funktionsappar delar samma `id` och var och en använder en timer som utlösare, endast en timer körs.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Kontroller som filtrering för loggar som skrivits av en [ILogger objekt](functions-monitoring.md#write-logs-in-c-functions) eller [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|categoryFilter|Saknas|Anger du filtrerar efter kategori| 
|defaultLevel|Information|För alla kategorier som angetts i den `categoryLevels` kan skicka loggar på den här nivån och senare till Application Insights.| 
|categoryLevels|Saknas|En matris med kategorier som anger den minsta Loggnivå som skickas till Application Insights för varje kategori. Den kategori som anges här styr alla kategorier som börjar med samma värde och längre värden ha företräde. I det föregående exemplet *host.json* filen, alla kategorier som börjar med ”Host.Aggregator”-loggen på `Information` nivå. Alla kategorier som börjar med ”värd”, till exempel ”Host.Executor” logga in på `Error` nivå.| 

## <a name="queues"></a>köer

Konfigurationsinställningar för [Storage-kö-utlösare och bindningar](functions-bindings-storage-queue.md).

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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximalt intervall i millisekunder mellan kön avsöker.| 
|visibilityTimeout|0|Det går inte att tidsintervall mellan försök vid bearbetning av ett meddelande.| 
|batchSize|16|Antal Kömeddelanden som Functions-körning hämtar samtidigt och bearbetar parallellt. När antalet bearbetas kommer ned till den `newBatchThreshold`, körningen får en annan batch och påbörjar bearbetningen av dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning för meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock samtidighet bara så länge som din funktionsapp körs på en enskild virtuell dator (VM). Om funktionsappen skalas ut till flera virtuella datorer, kan varje virtuell dator kör en instans av varje funktion som utlöses av kön.<br><br>Maximalt `batchSize` är 32. | 
|maxDequeueCount|5|Antal gånger att försöka bearbetar ett meddelande innan du flyttar den till skadliga kön.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetades samtidigt hamnar till det här talet, hämtar körningen en annan batch.| 

## <a name="servicebus"></a>serviceBus

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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till återanrop som meddelandet pump ska starta. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. För att dirigera körning för att bearbeta en enskild kö eller ett ämne meddelande i taget, ange `maxConcurrentCalls` till 1. | 
|prefetchCount|Saknas|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maximal varaktighet inom vilken meddelandelåset förnyas automatiskt.| 

## <a name="singleton"></a>Singleton-instans

Konfigurationsinställningar för Singleton-låsbeteende. Mer information finns i [GitHub-ärende om singleton-stöd](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktionen på Lås används för. Låsen den automatiska förnyelsen.| 
|listenerLockPeriod|00:01:00|Den period som lyssnare Lås används för.| 
|listenerLockRecoveryPollingInterval|00:01:00|Tidsintervall som används för lyssnare låsåterställning om lyssnaren låsa inte det gick att hämta vid start.| 
|lockAcquisitionTimeout|00:01:00|Längsta tid körningen kommer att försöka låsa.| 
|lockAcquisitionPollingInterval|Saknas|Intervall mellan försök för anskaffning av Lås.| 

## <a name="tracing"></a>spårning

*Version 1.x*

Konfigurationsinställningar för loggar som du skapar med hjälp av en `TraceWriter` objekt. Se [C# loggning](functions-reference-csharp.md#logging) och [Node.js loggning](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|consoleLevel|info|Spårningsnivån för konsolen loggning. Alternativen är: `off`, `error`, `warning`, `info`, och `verbose`.|
|fileLoggingMode|debugOnly|Spårningsnivån för filen loggning. Alternativen är `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

En uppsättning [delad kod kataloger](functions-reference-csharp.md#watched-directories) som ska övervakas avseende ändringar.  Säkerställer att när koden i dessa kataloger ändras ändringarna fångas upp av dina funktioner.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du uppdaterar filen host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Globala inställningar i miljövariabler](functions-app-settings.md)
