---
title: host.json-referens för Azure Functions 1.x
description: Referensdokumentation för Azure Functions host.json-filen med v1-körningen.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 2b00e2343e0959e07b195e2e98c6719a1893b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277055"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.json-referens för Azure Functions 1.x

> [!div class="op_single_selector" title1="Välj den version av Azure Functions-körningen som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Metadatafilen *host.json* innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. I den här artikeln visas de inställningar som är tillgängliga för v1-körningen. JSON-schemat är http://json.schemastore.org/hostpå .

> [!NOTE]
> Den här artikeln är avsedd för Azure Functions 1.x.  En referens för host.json i Functions 2.x och senare finns i [host.json-referens för Azure Functions 2.x](functions-host-json.md).

Andra funktionsappkonfigurationsalternativ hanteras i [appinställningarna](functions-app-settings.md).

Vissa host.json-inställningar används bara när de körs lokalt i filen [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>Exempel på värd.json-fil

Följande *exempelvärd.json-filer* har alla möjliga alternativ angivna.


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

I följande avsnitt i den här artikeln beskrivs varje egenskap på den översta nivån. Alla är frivilliga om inte annat anges.

## <a name="aggregator"></a>Nyhetsläsare

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>programInsikter

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Konfigurationsinställningar för [Azure Cosmos DB-utlösaren och bindningarna](functions-bindings-cosmosdb.md).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|GatewayMode (GatewayMode)|Gateway|Anslutningsläget som används av funktionen när du ansluter till Azure Cosmos DB-tjänsten. Alternativen `Direct` är och`Gateway`|
|Protokoll|Https( https)|Anslutningsprotokollet som används av funktionen vid anslutning till Azure Cosmos DB-tjänsten.  Läs [här för en förklaring av båda lägena](../cosmos-db/performance-tips.md#networking)|
|leasa Förefix|Saknas|Prefix för lån som ska användas för alla funktioner i en app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Konfigurationsinställningar för [utlösare och bindningar i eventhubben](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>Funktioner

En lista över funktioner som jobbvärden kör. En tom matris innebär att köra alla funktioner. Avsedd endast för användning när [du kör lokalt](functions-run-local.md). I funktionsappar i Azure bör du i stället följa stegen i Så här inaktiverar du [funktioner i Azure-funktioner](disable-function.md) för att inaktivera specifika funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funktionTimeout

Anger tidsgränsen för alla funktioner. I en serverlös förbrukningsplan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter. I en apptjänstplan finns det ingen total gräns och standardvärdet är _null_, vilket indikerar ingen timeout.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>hälsaMonitor

Konfigurationsinställningar för [hälsa övervakare av värd .](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor)

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|enabled|true|Anger om funktionen är aktiverad. | 
|hälsaKontrolleraInterval|10 sekunder|Tidsintervallet mellan de periodiska hälsokontrollerna i bakgrunden. | 
|hälsaKontrolleraFönster|2 minuter|Ett skjuttidsfönster som `healthCheckThreshold` används tillsammans med inställningen.| 
|hälsaKontrollerAReshold|6|Maximalt antal gånger hälsokontrollen kan misslyckas innan en värdåtervinning initieras.| 
|counterThreshold (300)|0,80|Det tröskelvärde vid vilket en prestandaräknare betraktas som ohälsosam.| 

## <a name="http"></a>http

Konfigurationsinställningar för [http-utlösare och bindningar](functions-bindings-http-webhook.md).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|När den här inställningen är aktiverad gör den att rörledningen för begäran bearbetning regelbundet kontrollera systemprestandaräknare som anslutningar /trådar/processer/minne/cpu/etc. och om någon av dessa räknare är över ett inbyggt högt tröskelvärde (80%), begäranden kommer att avvisas med ett 429 "För upptagen" svar tills räknarna(er) återgår till normala nivåer.|
|maxConcurrentRequests|obunden (`-1`)|Det maximala antalet HTTP-funktioner som ska köras parallellt. På så sätt kan du styra samtidighet, vilket kan hjälpa till att hantera resursutnyttjande. Du kan till exempel ha en HTTP-funktion som använder många systemresurser (minne/cpu/sockets) så att det orsakar problem när samtidigheten är för hög. Eller så kan du ha en funktion som gör utgående förfrågningar till en tjänst från tredje part, och dessa samtal måste vara begränsad. I dessa fall kan det vara till hjälp att använda en gasrep här.|
|maxOutstandingRequests|obunden (`-1`)|Det maximala antalet utestående begäranden som hålls vid en viss tidpunkt. Den här gränsen inkluderar begäranden som står i kö men inte har börjat köras, samt eventuella pågående körningar. Alla inkommande begäranden över den här gränsen avvisas med ett 429 "För upptagen"-svar. Det gör att de som ringer kan använda tidsbaserade strategier för återförsök och hjälper dig också att kontrollera maximal begärandenlighet. Detta styr bara kö som uppstår i skriptvärdkörningssökvägen. Andra köer som ASP.NET förfrågan-kön kommer fortfarande att gälla och påverkas inte av den här inställningen.|
|routePrefix|api|Flödesprefixet som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |

## <a name="id"></a>id

Det unika ID:t för en jobbvärd. Kan vara ett gemens GUID med streck borttagna. Krävs när du kör lokalt. När du kör i Azure rekommenderar vi att du inte anger ett ID-värde. Ett ID genereras automatiskt i `id` Azure när utelämnas. 

Om du delar ett lagringskonto över flera funktionsappar `id`kontrollerar du att varje funktionsapp har en annan . Du kan utelämna `id` egenskapen eller manuellt ställa `id` in varje funktionsapp till ett annat värde. Timerutlösaren använder ett lagringslås för att säkerställa att det bara finns en timerinstans när en funktionsapp skalas ut till flera instanser. Om två funktionsappar delar samma `id` och var och en använder en timerutlösare körs bara en timer.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Logger

Styr filtrering för loggar skrivna av ett [ILogger-objekt](functions-monitoring.md#write-logs-in-c-functions) eller [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|kategoriFilter|Saknas|Anger filtrering efter kategori| 
|standardNivå|Information|För alla kategorier som `categoryLevels` inte anges i matrisen skickar du loggar på den här nivån och uppåt till Application Insights.| 
|kategoriLevels|Saknas|En matris med kategorier som anger den lägsta loggnivån som ska skickas till Application Insights för varje kategori. Den kategori som anges här styr alla kategorier som börjar med samma värde, och längre värden har företräde. I föregående *exempelvärd.json-fil* loggar alla kategorier som börjar med "Host.Aggregator" på `Information` nivå. Alla andra kategorier som börjar med "Host", till exempel `Error` "Host.Executor", loggar på nivå.| 

## <a name="queues"></a>Köer

Konfigurationsinställningar för [utlösare och bindningar i lagringskön](functions-bindings-storage-queue.md).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|60000|Det maximala intervallet i millisekunder mellan köavsökningar.| 
|synlighetTimeout|0|Tidsintervallet mellan återförsöken när bearbetningen av ett meddelande misslyckas.| 
|batchSize|16|Antalet kömeddelanden som funktioner körs samtidigt och bearbetar parallellt. När numret som bearbetas kommer `newBatchThreshold`ner till , körtiden får en annan batch och börjar bearbeta dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas `batchSize` `newBatchThreshold`per funktion är plus . Den här gränsen gäller separat för varje köutlöst funktion. <br><br>Om du vill undvika parallell körning för meddelanden som `batchSize` tas emot i en kö kan du ställa in 1. Den här inställningen eliminerar dock samtidighet endast så länge som din funktionsapp körs på en enda virtuell dator (VM). Om funktionsappen skalas ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje köutlöst funktion.<br><br>Den `batchSize` maximala är 32. | 
|maxDequeueCount|5|Antalet gånger för att försöka bearbeta ett meddelande innan du flyttar det till giftkön.| 
|nyaBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt kommer ner till det här numret, hämtar körningen en annan batch.| 

## <a name="sendgrid"></a>SendGrid

Konfigurationsinställning för [SendGrind-utdatabindningen](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|Från|Saknas|Avsändarens e-postadress för alla funktioner.| 

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringningen som meddelandepumpen ska initiera. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. Om du vill att körningen ska bearbeta endast ett `maxConcurrentCalls` enskilt kö- eller ämnesmeddelande åt gången anger du till 1. | 
|prefetchCount (prefetchCount)|Saknas|StandardprefetchCount som ska användas av den underliggande MessageReceiver.| 
|autoRenewTimeout|00:05:00|Den maximala varaktighet inom vilken meddelandelåset förnyas automatiskt.| 

## <a name="singleton"></a>Singleton

Konfigurationsinställningar för Singleton-låsbeteende. Mer information finns i [GitHub-problemet om singleton-stöd](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktionsnivålås tas för. Låsen förnyas automatiskt.| 
|lyssnareLockPeriod|00:01:00|Den period som lyssnarlås tas för.| 
|lyssnareLockRecoveryPollingInterval|00:01:00|Tidsintervallet som används för återställning av lyssnarlås om ett lyssnarlås inte kunde hämtas vid start.| 
|lockAcquisitionTimeout|00:01:00|Den maximala tiden som körningen försöker hämta ett lås.| 
|lockAcquisitionPollingInterval|Saknas|Intervallet mellan låsförvärvförsök.| 

## <a name="tracing"></a>Spårning

*Version 1.x*

Konfigurationsinställningar för loggar som du `TraceWriter` skapar med hjälp av ett objekt. Se [C# Loggning](functions-reference-csharp.md#logging) och [Node.js Loggning](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|konsolLevel|information|Spårningsnivån för konsolloggning. `off`Alternativen är: `error` `warning`, `info`, `verbose`, och .|
|filLoggingMode|felsökaOnt|Spårningsnivån för filloggning. Alternativen `never`är `always` `debugOnly`, , .| 

## <a name="watchdirectories"></a>watchDirectories (watchDirectories)

En uppsättning [delade kodkataloger](functions-reference-csharp.md#watched-directories) som ska övervakas för ändringar.  Säkerställer att när koden i dessa kataloger ändras, hämtas ändringarna av dina funktioner.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du uppdaterar filen host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Se globala inställningar i miljövariabler](functions-app-settings.md)
