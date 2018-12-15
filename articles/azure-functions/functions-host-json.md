---
title: Host.JSON-referens för Azure Functions 2.x
description: Referensdokumentation för Azure Functions host.json filen med v2-runtime.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 4da64f01f3b4f39bd10fd3cb1b67910ffca886b8
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413276"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Host.JSON-referens för Azure Functions 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Den *host.json* metadatafilen innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. Den här artikeln innehåller de inställningar som är tillgängliga för v2-runtime.  

> [!NOTE]
> Den här artikeln är för Azure Functions 2.x.  En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

Andra konfigurationsalternativ för funktionen app hanteras i din [appinställningar](functions-app-settings.md).

Vissa host.json-inställningarna används bara när du kör lokalt i den [local.settings.json](functions-run-local.md#local-settings-file) fil.

## <a name="sample-hostjson-file"></a>Exempelfilen för host.json

I följande exempel *host.json* filer har alla möjliga alternativ som har angetts.


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

I följande avsnitt i den här artikeln beskrivs varje översta egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>Aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Den här inställningen är underordnad [loggning](#logging).

Kontroller i [sampling funktion i Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
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

## <a name="cosmosdb"></a>cosmosDb

Konfigurationsinställningen kan hittas i [Cosmos DB-utlösare och bindningar](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

Konfigurationsinställningen kan hittas i [bindningar för varaktiga funktioner](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Konfigurationsinställningar finns i [Event Hub-utlösare och bindningar](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Tillägg

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

Konfigurationsinställningar finns i [http-utlösare och bindningar](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Loggning

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
|fileLoggingMode|debugOnly|Definierar vilken nivå av filen loggning är aktiverat.  Alternativen är `never`, `always`, `debugOnly`. |
|LogLevel|Saknas|Objekt som definierar loggkategori filtrering för funktionerna i appen. Version 2.x följer ASP.NET Core-layouten för log kategorifiltrering. På så sätt kan du filtrera loggning för specifika funktioner. Mer information finns i [Log filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) i ASP.NET Core-dokumentationen. |
|konsol|Saknas| Den [konsolen](#console) inställning för aktivitetsloggning. |
|applicationInsights|Saknas| Den [applicationInsights](#applicationinsights) inställningen. |

## <a name="console"></a>konsol

Den här inställningen är underordnad [loggning](#logging). Den kontrollerar konsolen loggning när inte i felsökningsläge.

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

|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|isEnabled|false|Aktiverar eller inaktiverar loggning för konsolen.| 

## <a name="queues"></a>köer

Konfigurationsinställningar finns i [Storage-kö-utlösare och bindningar](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>SendGrid

Konfigurationsinställningen kan hittas i [SendGrid utlösare och bindningar](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Konfigurationsinställningen kan hittas i [Service Bus-utlösare och bindningar](functions-bindings-service-bus.md#host-json).

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

## <a name="version"></a>version

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
