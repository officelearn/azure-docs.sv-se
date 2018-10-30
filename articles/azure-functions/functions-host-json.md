---
title: Host.JSON-referens för Azure Functions
description: Referensdokumentation för Azure Functions host.json-filen.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 7a049a1fb57d65824fa2c44b40d3d51083459973
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232264"
---
# <a name="hostjson-reference-for-azure-functions"></a>Host.JSON-referens för Azure Functions

Den *host.json* metadatafilen innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. Den här artikeln innehåller de inställningar som är tillgängliga. JSON-schemat är i http://json.schemastore.org/host.

> [!NOTE]
> Det finns betydande skillnader i den *host.json* mellan versioner v1 och v2 av Azure Functions-körningen. Den `"version": "2.0"` krävs för en funktionsapp som riktar sig mot v2-runtime.

Andra konfigurationsalternativ för funktionen app hanteras i din [appinställningar](functions-app-settings.md).

Vissa host.json-inställningarna används bara när du kör lokalt i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="sample-hostjson-file"></a>Exempelfilen för host.json

I följande exempel *host.json* filer har alla möjliga alternativ som har angetts.

### <a name="version-2x"></a>Version 2.x

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "eventHubs": {
          "maxBatchSize": 64,
          "prefetchCount": 256,
          "batchCheckpointFrequency": 1
        },
        "http": {
            "routePrefix": "api",
            "maxConcurrentRequests": 100,
            "maxOutstandingRequests": 30
        },
        "queues": {
            "visibilityTimeout": "00:00:10",
            "maxDequeueCount": 3
        },
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        },
        "serviceBus": {
          "maxConcurrentCalls": 16,
          "prefetchCount": 100,
          "autoRenewTimeout": "00:05:00"
        }
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
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "sampling": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

### <a name="version-1x"></a>Version 1.x

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

Anger hur många funktionsanrop är aggregeras när [beräkning av mätvärden för Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Egenskap  |Standard  | Beskrivning |
|---------|---------|---------| 
|batchSize|1000|Maximalt antal begäranden ska aggregeras.| 
|flushTimeout|00:00:30|Maximal tid period ska aggregeras.| 

Funktionsanrop räknas samman när först av två begränsar uppnås.

## <a name="applicationinsights"></a>applicationInsights

Kontroller i [sampling funktion i Application Insights](functions-monitoring.md#configure-sampling). I version 2.x kan den här inställningen är underordnad [loggning](#logging).

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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|isEnabled|true|Aktiverar eller inaktiverar sampling.| 
|maxTelemetryItemsPerSecond|5|Tröskelvärdet på vilka sampling börjar.| 

## <a name="durabletask"></a>durableTask

Konfigurationsinställningar för [varaktiga funktioner](durable-functions-overview.md).

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

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges är standardnamnet på uppgiften hub för en funktionsapp **DurableFunctionsHub**. Mer information finns i [uppgift hubs](durable-functions-task-hubs.md).

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Alternativa [uppgift hub](durable-functions-task-hubs.md) namn kan användas för att isolera program för flera varaktiga funktioner från varandra, även om artikelserien med samma lagring-serverdelen.|
|ControlQueueBatchSize|32|Antal meddelanden att hämta från kön kontroll i taget.|
|PartitionCount |4|Antalet partitioner för kön kontroll. Kan vara ett positivt heltal mellan 1 och 16.|
|ControlQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön kontroll Kömeddelanden.|
|WorkItemQueueVisibilityTimeout |5 minuter|Synlighetstimeout för tagits bort från kön work item Kömeddelanden.|
|MaxConcurrentActivityFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet Aktivitetsfunktioner som kan bearbetas samtidigt på en enda värd-instans.|
|MaxConcurrentOrchestratorFunctions |10 gånger antalet processorer på den aktuella datorn|Det maximala antalet Aktivitetsfunktioner som kan bearbetas samtidigt på en enda värd-instans.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Namnet på den appinställning som har Azure Storage-anslutningssträng som används för att hantera de underliggande Azure-lagringsresurserna.|
|TraceInputsAndOutputs |false|Ett värde som anger om du vill spåra indata och utdata av funktionsanrop. Standardbeteende när spårning av händelser för körning av funktionen är att inkludera antalet byte i serialiserade indata och utdata för funktionsanrop. Detta ger minimalt med information om hur indata och utdata ut utan svullen buk loggarna eller av misstag exponera känslig information till loggarna. Denna egenskap anges till true kommer funktionen Standardloggning logga hela innehållet i funktionen indata och utdata.|
|LogReplayEvents|false|Ett värde som anger om du vill skriva orchestration repetitionsattacker händelser till Application Insights.|
|EventGridTopicEndpoint ||URL till en slutpunkt för Azure Event Grid-anpassat ämne. När den här egenskapen anges publiceras orchestration livscykel meddelandehändelser till den här slutpunkten. Den här egenskapen stöder Appinställningar skärmupplösning.|
|EventGridKeySettingName ||Namnet på den appinställning som innehåller den nyckel som används för att autentisera med det anpassade ämnet Azure Event Grid på `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Det går inte att antalet gånger att försöka igen om publicering till Event Grid-ämne.|
|EventGridPublishRetryInterval|5 minuter|Event Grid publicerar återförsöksintervallet i den *: mm: ss* format.|

Många av dessa är för att optimera prestanda. Mer information finns i [prestanda och skalning](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Konfigurationsinställningar för [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md). I version 2.x kan det här är en underordnad [tillägg](#extensions).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="extensions"></a>Tillägg

*Version 2.x endast.*

Egenskap som returnerar ett objekt som innehåller alla bindning-specifika inställningar, till exempel [http](#http) och [eventHub](#eventhub).

## <a name="functions"></a>functions

En lista över funktioner som värd för jobb körs. En tom matris innebär att köra alla funktioner. Avsedd att användas endast när [körs lokalt](functions-run-local.md). I funktionsappar i Azure, bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) att inaktivera specifika funktioner i stället för den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger timeout-varaktighet för alla funktioner. Det giltiga intervallet är mellan 1 sekund till 10 minuter i en serverlös förbrukningsplan och standardvärdet är 5 minuter. Det finns ingen övergripande gräns i en App Service-plan och standard beroende av runtime-versionen. I version 2.x, standardvärdet för en App Service-planen är 30 minuter. I version 1.x, är det *null*, vilket anger att ingen tidsgräns.

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

Konfigurationsinställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md). I version 2.x kan det här är en underordnad [tillägg](#extensions).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Version 1.x endast.*

Unikt ID för en värd för jobbet. Kan vara en gemen GUID med bindestreck tas bort. Krävs när du kör lokalt. När du kör i Azure rekommenderar vi att du inte anger ett ID-värde. Ett ID genereras automatiskt i Azure när `id` utelämnas. Du kan inte ange en anpassad funktion app-ID när du använder version 2.x-körningen.

Om du delar ett Storage-konto i flera funktionsappar, se till att varje funktionsapp har en annan `id`. Du kan utelämna den `id` egenskapen eller manuellt ange varje funktionsapp `id` på ett annat värde. Timer utlösaren använder ett storage-Lås för att se till att endast en timer-instans när en funktionsapp skalas ut till flera instanser. Om två funktionsappar delar samma `id` och var och en använder en timer som utlösare, endast en timer körs.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>loggaren

*Version 1.x. för version 2.x användning [loggning](#logging).*

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

## <a name="logging"></a>Loggning

*Version 2.x. för version 1.x användning [loggaren](#logger).*

Styr loggning beteenden för funktionsappen, inklusive Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------|
|fileLoggingMode|information|Skickar loggar på den här nivån och senare till Application Insights. |
|LogLevel|Saknas|Objekt som definierar loggkategori filtrering för funktionerna i appen. Version 2.x följer ASP.NET Core-layouten för log kategorifiltrering. På så sätt kan du filtrera loggning för specifika funktioner. Mer information finns i [Log filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) i ASP.NET Core-dokumentationen. |
|applicationInsights|Saknas| Den [applicationInsights](#applicationinsights) inställningen. |

## <a name="queues"></a>köer

Konfigurationsinställningar för [Storage-kö-utlösare och bindningar](functions-bindings-storage-queue.md). I version 2.x kan det här är en underordnad [tillägg](#extensions).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Konfigurationsinställning för [Service Bus-utlösare och bindningar](functions-bindings-service-bus.md). I version 2.x kan det här är en underordnad [tillägg](#extensions).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

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

Konfigurationsinställningar för loggar som du skapar med hjälp av en `TraceWriter` objekt. Se [C# loggning](functions-reference-csharp.md#logging) och [Node.js loggning](functions-reference-node.md#writing-trace-output-to-the-console). I version 2.x kan alla log beteende styrs av [loggning](#logging).

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

## <a name="version"></a>version

*Version 2.x*

Versionsträngen `"version": "2.0"` krävs för en funktionsapp som riktar sig mot v2-runtime.

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
