---
title: Host. JSON-referens för Azure Functions 2. x
description: Referens dokumentation för Azure Functions Host. JSON-fil med v2-körningsmiljön.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: 03abacf6bb18a4d3b6e9b01328806d2dcb6971e1
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304800"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Host. JSON-referens för Azure Functions 2. x  

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

*Host. JSON* -metadatafilen innehåller globala konfigurations alternativ som påverkar alla funktioner för en Function-app. I den här artikeln visas de inställningar som är tillgängliga för v2-körningen.  

> [!NOTE]
> Den här artikeln är för Azure Functions 2. x.  En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

Andra konfigurations alternativ för Function-appar hanteras i dina [app-inställningar](functions-app-settings.md).

Vissa värden. JSON-inställningar används bara när de körs lokalt i den [lokala. Settings. JSON](functions-run-local.md#local-settings-file) -filen.

## <a name="sample-hostjson-file"></a>Exempel på Host. JSON-fil

Följande exempel på *Host. JSON* -filer har alla möjliga alternativ angivna.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
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

I följande avsnitt i den här artikeln beskrivs varje toppnivå egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>aggregera

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Den här inställningen är underordnad [loggning](#logging).

Styr [samplings funktionen i Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Logg sampling kan orsaka att vissa körningar inte visas på bladet Application Insights övervakning.

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|isEnabled|true|Aktiverar eller inaktiverar sampling.| 
|maxTelemetryItemsPerSecond|20|Tröskelvärdet då samplingen börjar.| 
|EnableLiveMetrics |true|Aktiverar insamling av Live-mått.|
|EnableDependencyTracking|true|Aktiverar beroende spårning.|
|EnablePerformanceCountersCollection|true|Aktiverar insamling av kudu prestanda räknare.|

## <a name="cosmosdb"></a>cosmosDb

Du hittar konfigurations inställningen i [Cosmos DB utlösare och bindningar](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Konfigurations inställningen finns i [bindningar för Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Konfigurations inställningar finns i [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>tillägg

Egenskap som returnerar ett objekt som innehåller alla bindande inställningar, till exempel [http](#http) och [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Med tilläggs paket kan du lägga till en kompatibel uppsättning funktions bindnings tillägg i din app. Mer information finns i [tilläggs paket för lokal utveckling](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

En lista med funktioner som jobb värden kör. En tom matris innebär att köra alla-funktioner. Endast avsedd att användas när du [Kör lokalt](functions-run-local.md). I Function-appar i Azure bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) för att inaktivera vissa funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger varaktigheten för alla funktioner. Det följer sträng formatet TimeSpan. I en server lös förbruknings plan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter.  
I en dedikerad (App Service) plan finns det ingen övergripande gräns och standardvärdet är 30 minuter. Ett värde på `-1` indikerar obegränsad körning, men en fast övre bindning rekommenderas.

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
|enabled|true|Anger om funktionen är aktive rad. | 
|healthCheckInterval|10 sekunder|Tidsintervallet mellan de regelbundna hälso kontrollerna i bakgrunden. | 
|healthCheckWindow|2 minuter|Ett glidande tids fönster som används tillsammans med `healthCheckThreshold`-inställningen.| 
|healthCheckThreshold|6|Maximalt antal gånger som hälso kontrollen kan återställas innan en återkallning av en värd initieras.| 
|counterThreshold|0,80|Tröskelvärdet som en prestanda räknare kommer att anses vara ohälsosam.| 

## <a name="http"></a>http

Konfigurations inställningar kan hittas i [http-utlösare och bindningar](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>Logging

Styr loggnings beteenden för Function-appen, inklusive Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definierar vilken nivå av fil loggning som är aktive rad.  Alternativen är `never`, `always``debugOnly`. |
|logLevel|Saknas|Objekt som definierar logg kategori filtrering för funktioner i appen. Version 2. x följer ASP.NET Core layout för filtrering av loggnings kategorier. På så sätt kan du filtrera loggning för vissa funktioner. Mer information finns i [logg filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) i ASP.net Core-dokumentationen. |
|konsol|Saknas| Den [console](#console) inställning för aktivitetsloggning. |
|applicationInsights|Saknas| Inställningen [applicationInsights](#applicationinsights) . |

## <a name="console"></a>konsol

Den här inställningen är underordnad [loggning](#logging). Den styr konsol loggningen när det inte är i fel söknings läge.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|isEnabled|false|Aktiverar eller inaktiverar konsol loggning.| 

## <a name="manageddependency"></a>managedDependency

Hanterat beroende är en funktion som för närvarande endast stöds med PowerShell-baserade funktioner. Det gör att beroenden kan hanteras automatiskt av tjänsten. När `enabled`-egenskapen är inställd på `true`bearbetas `requirements.psd1`s filen. Beroenden uppdateras när eventuella del versioner släpps. Mer information finns i [hanterat beroende](functions-reference-powershell.md#dependency-management) i PowerShell-artikeln.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>kön

Du hittar konfigurations inställningar i [utlösare och bindningar för lagrings köer](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Det går att hitta konfigurations inställningen i [SendGrid-utlösare och bindningar](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Du hittar konfigurations inställningen i [Service Bus utlösare och bindningar](functions-bindings-service-bus.md#host-json).

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
|lockAcquisitionPollingInterval|Saknas|Intervallet mellan lås försök.| 

## <a name="version"></a>version

Versions strängen `"version": "2.0"` krävs för en Function-app som är riktad mot v2-körningen.

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
