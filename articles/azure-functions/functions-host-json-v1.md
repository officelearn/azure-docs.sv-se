---
title: Host. JSON-referens för Azure Functions 1. x
description: Referens dokumentation för Azure Functions Host. JSON-fil med v1-körning.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 3d6a28c8cdcf13dc805d70832ed65732911138cd
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263346"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Host. JSON-referens för Azure Functions 1. x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

*Host. JSON* -metadatafilen innehåller globala konfigurations alternativ som påverkar alla funktioner för en Function-app. I den här artikeln visas de inställningar som är tillgängliga för v1-körningen. JSON-schemat är på http://json.schemastore.org/host.

> [!NOTE]
> Den här artikeln är för Azure Functions 1. x.  En referens för Host. json i functions 2. x finns i [Host. JSON-referensen för Azure Functions 2. x](functions-host-json.md).

Andra konfigurations alternativ för Function-appar hanteras i dina [app-inställningar](functions-app-settings.md).

Vissa värden. JSON-inställningar används bara när de körs lokalt i den [lokala. Settings. JSON](functions-run-local.md#local-settings-file) -filen.

## <a name="sample-hostjson-file"></a>Exempel på Host. JSON-fil

Följande exempel på *Host. JSON* -filer har alla möjliga alternativ angivna.


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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
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

I följande avsnitt i den här artikeln beskrivs varje toppnivå egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>aggregera

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Konfigurations inställningar för [Azure Cosmos DB utlösare och bindningar](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|GatewayMode|Gateway|Anslutnings läget som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten. Alternativen är `Direct` och `Gateway`|
|Protokoll|https|Anslutnings protokollet som används av funktionen vid anslutning till Azure Cosmos DBs tjänsten.  Läs [här om du vill ha en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Ej tillämpligt|Lease-prefix som ska användas för alla funktioner i en app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Konfigurations inställningar för [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

En lista med funktioner som jobb värden kör. En tom matris innebär att köra alla-funktioner. Endast avsedd att användas när du [Kör lokalt](functions-run-local.md). I Function-appar i Azure bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) för att inaktivera vissa funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger varaktigheten för alla funktioner. I en server lös förbruknings plan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter. I en App Service plan finns det ingen övergripande gräns och Standardvärdet beror på körnings versionen.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Konfigurations inställningar för [övervakaren av värd hälsa](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|aktiva|sant|Anger om funktionen är aktive rad. | 
|healthCheckInterval|10 sekunder|Tidsintervallet mellan de regelbundna hälso kontrollerna i bakgrunden. | 
|healthCheckWindow|2 minuter|Ett glidande tids fönster som används tillsammans med inställningen `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maximalt antal gånger som hälso kontrollen kan återställas innan en återkallning av en värd initieras.| 
|counterThreshold|0,80|Tröskelvärdet som en prestanda räknare kommer att anses vara ohälsosam.| 

## <a name="http"></a>http

Konfigurations inställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Endast version 1. x.*

Unikt ID för en jobb värd. Kan vara ett GUID för gemener/versaler med streck borttagna. Krävs när du kör lokalt. När du kör i Azure rekommenderar vi att du inte anger något ID-värde. Ett ID genereras automatiskt i Azure när `id` utelämnas. 

Om du delar ett lagrings konto över flera Function-appar, se till att varje Function-app har en annan `id`. Du kan utelämna egenskapen `id` eller manuellt ange varje funktions programs `id` till ett annat värde. Timer-utlösaren använder ett lagrings lås för att säkerställa att det bara kommer att finnas en timer-instans när en Function-app skalar ut till flera instanser. Om två Function-appar delar samma `id` och varje använder en timer-utlösare, körs bara en timer.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Loggar

Styr filtrering av loggar som skrivits av ett [ILogger-objekt](functions-monitoring.md#write-logs-in-c-functions) eller av [context. log](functions-monitoring.md#write-logs-in-javascript-functions).

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
|categoryFilter|Ej tillämpligt|Anger filtrering efter kategori| 
|defaultLevel|Information|För alla kategorier som inte anges i matrisen `categoryLevels`, skicka loggar på den här nivån och ovan för att Application Insights.| 
|categoryLevels|Ej tillämpligt|En matris med kategorier som anger den minsta logg nivå som ska skickas till Application Insights för varje kategori. Den kategori som anges här styr alla kategorier som börjar med samma värde och värden som är längre prioriterade. I föregående exempel *Host. JSON* -fil är alla kategorier som börjar med "Host. aggregator"-logg på `Information`-nivå. Alla andra kategorier som börjar med "värd", till exempel "Host. utförar", log på `Error`-nivå.| 

## <a name="queues"></a>kön

Konfigurations inställningar för [utlösare och bindningar i lagrings kön](functions-bindings-storage-queue.md).

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
|maxPollingInterval|60000|Det maximala intervallet i millisekunder mellan Queue-avsökningar.| 
|visibilityTimeout|0|Tidsintervall mellan återförsök vid bearbetning av ett meddelande Miss lyckas.| 
|batchSize|16|Antalet köa meddelanden som funktions körningen hämtar samtidigt och processer parallellt. När antalet som bearbetas går ned till `newBatchThreshold`, får körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning av meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock ingen samtidighet så länge som din funktions App körs på en enda virtuell dator (VM). Om Function-appen skalar ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje funktion som utlöses av kön.<br><br>Högsta `batchSize` är 32. | 
|maxDequeueCount|5|Antal försök att bearbeta ett meddelande innan det flyttas till en Poison-kö.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt går till det här talet, hämtar körningen en annan batch.| 

## <a name="sendgrid"></a>SendGrid

Konfigurations inställning för [SendGrind utgående bindning](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|som|Ej tillämpligt|Avsändarens e-postadress för alla funktioner.| 

## <a name="servicebus"></a>Service Bus

Konfigurations inställning för [Service Bus utlösare och bindningar](functions-bindings-service-bus.md).

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
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringning som meddelande pumpen ska initiera. Som standard bearbetar Functions-körningen flera meddelanden samtidigt. Ange `maxConcurrentCalls` till 1 för att dirigera körningen för att endast bearbeta en enskild kö eller ett ämne i taget. | 
|prefetchCount|Ej tillämpligt|Standard-PrefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Den längsta tid som meddelande låset ska förnyas automatiskt.| 

## <a name="singleton"></a>Singleton

Konfigurations inställningar för beteendet singleton lock. Mer information finns i [GitHub problem med singleton-stöd](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktions nivå lås utförs för. Lås automatisk förnyelse.| 
|listenerLockPeriod|00:01:00|Den period som lyssnarens lås tas för.| 
|listenerLockRecoveryPollingInterval|00:01:00|Det tidsintervall som används för återställning av lyssnar lås om det inte gick att hämta ett lyssnar lås vid start.| 
|lockAcquisitionTimeout|00:01:00|Den maximala tid som körningen kommer att försöka hämta ett lås.| 
|lockAcquisitionPollingInterval|Ej tillämpligt|Intervallet mellan lås försök.| 

## <a name="tracing"></a>spårning

*Version 1. x*

Konfigurations inställningar för loggar som du skapar med hjälp av ett `TraceWriter`-objekt. Se [ C# loggning](functions-reference-csharp.md#logging) och [Node. js-loggning](functions-reference-node.md#writing-trace-output-to-the-console).

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
|consoleLevel|statusinformation|Spårnings nivån för konsol loggning. Alternativen är: `off`, `error`, `warning`, `info` och `verbose`.|
|fileLoggingMode|debugOnly|Spårnings nivån för fil loggning. Alternativen är `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

En uppsättning [delade kod kataloger](functions-reference-csharp.md#watched-directories) som ska övervakas för ändringar.  Säkerställer att när kod i dessa kataloger ändras, hämtas ändringarna av funktionerna.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du uppdaterar Host. JSON-filen](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Se globala inställningar i miljövariabler](functions-app-settings.md)
