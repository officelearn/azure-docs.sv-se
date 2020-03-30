---
title: host.json-referens för Azure Functions 2.x
description: Referensdokumentation för Azure Functions host.json-filen med v2-körningen.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3d98be2dcc351aa88b9e126c883865079e407c2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473378"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>host.json-referens för Azure Functions 2.x och senare 

> [!div class="op_single_selector" title1="Välj den version av Azure Functions-körningen som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2+](functions-host-json.md)

Metadatafilen *host.json* innehåller globala konfigurationsalternativ som påverkar alla funktioner för en funktionsapp. I den här artikeln visas de inställningar som är tillgängliga från och med version 2.x av Azure Functions-körningen.  

> [!NOTE]
> Den här artikeln är avsedd för Azure Functions 2.x och senare versioner.  En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

Andra funktionsappkonfigurationsalternativ hanteras i [appinställningarna](functions-app-settings.md) (för distribuerade appar) eller filen [local.settings.json](functions-run-local.md#local-settings-file) (för lokal utveckling).

Konfigurationer i host.json relaterade till bindningar tillämpas lika på varje funktion i funktionsappen. 

## <a name="sample-hostjson-file"></a>Exempel på värd.json-fil

Följande *exempelvärd.json-fil* för version 2.x+ har alla möjliga alternativ angivna (exklusive alternativ som endast är avsedda för internt bruk).

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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
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

I följande avsnitt i den här artikeln beskrivs varje egenskap på den översta nivån. Alla är frivilliga om inte annat anges.

## <a name="aggregator"></a>Nyhetsläsare

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>programInsikter

Den här inställningen är underordnad [loggning](#logging).

Styr alternativ för Application Insights, inklusive [samplingsalternativ](./functions-monitoring.md#configure-sampling).

För den fullständiga JSON-strukturen finns i den tidigare [exempelvärden.json-filen](#sample-hostjson-file).

> [!NOTE]
> Loggs sampling kan orsaka att vissa körningar inte visas i övervakarbladet Application Insights. Lägg till `samplingExcludedTypes: "Request"` `applicationInsights` värdet för att undvika loggprovtagning.

| Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| provtagningStiseringar | Saknas | Se [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| aktiveraLiveMetrics | true | Aktiverar insamling av aktiva mått. |
| enableDependencyTracking | true | Aktiverar beroendespårning. |
| aktiveraPerformanceCountersCollection | true | Aktiverar Kudu prestandaräknare samling. |
| liveMetricsInitializationDelay | 00:00:15 | Endast för internt bruk. |
| httpAutoCollectionOptions | Saknas | Se [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotKonfigurering | Saknas | Se [applicationInsights.snapshotKonfigurering](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| ärEnabled | true | Aktiverar eller inaktiverar sampling. | 
| maxTelemetryItemsPerSecond | 20 | Målantalet telemetriobjekt som loggas per sekund på varje servervärd. Om din app körs på många värdar minskar du det här värdet så att det ligger inom den totala trafiknivån. | 
| utvärderingInterval | 01:00:00 | Det intervall med vilket den aktuella telemetrihastigheten omvärderas. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta detta intervall om din telemetri kan leda till plötsliga skurar. |
| initialSamplingPercentage| 1.0 | Den första provtagningsprocent som tillämpades i början av provtagningsprocessen för att dynamiskt variera procentandelen. Minska inte värdet när du felsöker. |
| samplingPercentageÖmashTimeout | 00:00:01 | När samplingsprocentvärdet ändras avgör den här egenskapen hur snart därefter Application Insights tillåts höja samplingsprocenten igen för att samla in mer data. |
| samplingPercentageDecreaseTimeout | 00:00:01 | När samplingsprocentvärdet ändras avgör den här egenskapen hur snart därefter Application Insights tillåts sänka samplingsprocenten igen för att samla in mindre data. |
| minSamplingPercentage | 0,1 | När samplingsprocenten varierar bestämmer den här egenskapen den minsta tillåtna samplingsprocenten. |
| maxSamplingPercentage | 0,1 | När samplingsprocenten varierar bestämmer den här egenskapen den högsta tillåtna samplingsprocenten. |
| flyttaAverageRatio | 1.0 | Vid beräkningen av det glidande medelvärdet har den vikt som tilldelats det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Mindre värden gör algoritmen mindre reaktiv vid plötsliga ändringar. |
| excludedTypes | null | En semikolonavgränsad lista över typer som du inte vill sampla. Erkända typer är: Beroende, Händelse, Undantag, PageView, Request, Trace. Alla instanser av de angivna typerna överförs. de typer som inte har angetts samplas. |
| includedTypes | null | En semikolonavgränsad lista över typer som du vill sampla. en tom lista innebär alla typer. Typ som `excludedTypes` anges i åsidosättningstyper som visas här. Erkända typer är: Beroende, Händelse, Undantag, PageView, Request, Trace. Alla instanser av de angivna typerna överförs. de typer som inte har angetts samplas. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| aktiveraHttpTriggerExtendedInfoCollection | true | Aktiverar eller inaktiverar utökad HTTP-begärandeinformation för HTTP-utlösare: korrelationshuvuden för inkommande begäran, stöd för stöd för multiinstrumenteringsnycklar, HTTP-metod, sökväg och svar. |
| aktiveraW3CDistributedTracing | true | Aktiverar eller inaktiverar stöd för W3C-distribuerade spårningsprotokoll (och aktiverar äldre korrelationsschema). Aktiverad som `enableHttpTriggerExtendedInfoCollection` standard om den är sann. Om `enableHttpTriggerExtendedInfoCollection` är falskt gäller den här flaggan endast för utgående begäranden, inte inkommande begäranden. |
| aktiveraResponseHeaderInjection | true | Aktiverar eller inaktiverar injektion av korrelationshuvuden med flera komponenter i svar. Om du aktiverar injektion kan Application Insights konstruera en programkarta till när flera instrumenteringsnycklar används. Aktiverad som `enableHttpTriggerExtendedInfoCollection` standard om den är sann. Den här inställningen gäller `enableHttpTriggerExtendedInfoCollection` inte om den är falsk. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotKonfigurering

Mer information om ögonblicksbilder finns [i Felsöka ögonblicksbilder om undantag i .NET-appar](/azure/azure-monitor/app/snapshot-debugger) och [felsöka problem med att aktivera Ögonblicksbildfelsökare för ögonblicksbilder av programinsikter eller visa ögonblicksbilder](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| agentEndpoint | null | Slutpunkten som används för att ansluta till tjänsten Ögonblicksbildsfelsökare för programinsikter. Om null används en standardslutpunkt. |
| captureSnapshotMemoryVikt | 0,5 | Vikten som ges till den aktuella processminnesstorleken när du kontrollerar om det finns tillräckligt med minne för att ta en ögonblicksbild. Det förväntade värdet är en större än 0 rätt fraktion (0 < CaptureSnapshotMemoryWeight < 1). |
| misslyckadesRequestLimit | 3 | Gränsen för antalet misslyckade begäranden om att begära ögonblicksbilder innan telemetriprocessorn inaktiveras.|
| handleUntrackedExceptions | true | Aktiverar eller inaktiverar spårning av undantag som inte spåras av telemetrin Application Insights. |
| ärEnabled | true | Aktiverar eller inaktiverar ögonblicksbildsamling | 
| ärEnabledInDeveloperMode | false | Aktiverar eller inaktiverar ögonblicksbildsamling aktiveras i utvecklarläge. |
| ärEnabledWhenProfiling | true | Aktiverar eller inaktiverar att ögonblicksbild skapas även om Application Insights Profiler samlar in en detaljerad profileringssession. |
| ärExceptionSnappointsEnabled | false | Aktiverar eller inaktiverar filtrering av undantag. |
| ärLowPrioritySnapshotUploader | true | Avgör om snapshotuploader-processen ska köras under normal prioritet. |
| maximumCollectionPlanSize | 50 | Det maximala antalet problem som vi kan spåra när som helst i ett intervall från en till 9999. |
| maximumSnapshotsRequired maximumSnapshotsRequired maximumSnapshotsRequired maximumS | 3 | Det maximala antalet ögonblicksbilder som samlats in för ett enskilt problem, i ett intervall från en till 999. Ett problem kan ses som en individuell kasta uttalande i din ansökan. När antalet ögonblicksbilder som samlats in för ett problem når det här värdet samlas inga fler ögonblicksbilder `problemCounterResetInterval`in `thresholdForSnapshotting` för det problemet förrän problemräknare återställs (se ) och gränsen nås igen. |
| problemCounterResetInterval | 24:00:00 | Hur ofta du återställer problemräknarna på en minut från en minut till sju dagar. När det här intervallet har uppnåtts återställs alla problemantal till noll. Befintliga problem som redan har nått tröskelvärdet för att göra ögonblicksbilder, `maximumSnapshotsRequired`men som ännu inte har genererat antalet ögonblicksbilder i , förblir aktiva. |
| geAnonymousTelemetry | true | Avgör om anonym användning och feltelemetri ska skickas till Microsoft. Den här telemetrin kan användas om du kontaktar Microsoft för att felsöka problem med felsökaren för ögonblicksbilder. Det används också för att övervaka användningsmönster. |
| återanslutningInkoppla | 00:15:00 | Hur ofta vi återansluter till slutpunkten Ögonblicksbildfelsökare. Tillåtet intervall är en minut till en dag. |
| shadowCopyFolder | null | Anger den mapp som ska användas för skuggkopieringsbinärer. Om inte anges, de mappar som anges av följande miljö variabler provas i ordning: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Om det är sant samlar bara en instans av SnapshotUploader in och laddar upp ögonblicksbilder för flera appar som delar InstrumentationKey. Om den är inställd på false kommer SnapshotUploader att vara unik för varje (ProcessName, InstrumentationKey) tuppel. |
| snapshotInLowPriorityDläs | true | Avgör om ögonblicksbilder ska bearbetas i en tråd med låg I/IO-prioritet eller inte. Att skapa en ögonblicksbild är en snabb åtgärd, men för att ladda upp en ögonblicksbild till tjänsten Ögonblicksbildfelsökare måste den först skrivas till disken som en minidump. Det händer i SnapshotUploader-processen. Om du ställer in det här värdet på true används I/o med låg prioritet för att skriva minidumpen, som inte konkurrerar med ditt program för resurser. Om du anger det här värdet för att false snabbar upp minidump-skapandet på bekostnad av att programmet har saktats ned. |
| snapshotsPerDayLimit | 30 | Det maximala antalet ögonblicksbilder som tillåts på en dag (24 timmar). Den här gränsen tillämpas också på tjänstsidan för Application Insights. Uppladdningar är begränsad till 50 per dag per program (det vill ha per instrumenteringsnyckel). Det här värdet förhindrar att skapa ytterligare ögonblicksbilder som så småningom kommer att avvisas under överföringen. Värdet noll tar bort gränsen helt, vilket rekommenderas inte. |
| snapshotsPerTenMinutesLimit | 1 | Det maximala antalet ögonblicksbilder som tillåts på 10 minuter. Även om det inte finns någon övre gräns för det här värdet, var försiktig med att öka det på produktionsarbetsbelastningar eftersom det kan påverka prestanda för ditt program. Det går snabbt att skapa en ögonblicksbild av ögonblicksbilden och ladda upp den till tjänsten Snapshot Debugger är en mycket långsammare åtgärd som kommer att konkurrera med ditt program för resurser (både CPU och I/O). |
| tempFolder (tempFolder) | null | Anger mappen för att skriva minidumps och uploader loggfiler. Om den inte är inställd används *%TEMP%\Dumpar.* |
| thresholdForSnapshotting | 1 | Hur många gånger Application Insights behöver se ett undantag innan den ber om ögonblicksbilder. |
| uploaderProxy | null | Åsidosätter proxyservern som används i processen för uppladdare för ögonblicksbilder. Du kan behöva använda den här inställningen om programmet ansluter till internet via en proxyserver. Snapshot Collector körs i programmets process och använder samma proxyinställningar. Snapshot Uploader körs dock som en separat process och du kan behöva konfigurera proxyservern manuellt. Om det här värdet är null försöker Snapshot Collector att automatiskt granska proxyns adress genom att undersöka System.Net.WebRequest.DefaultWebProxy och överföra värdet till Snapshot Uploader. Om det här värdet inte är null används inte automatisk avdetection och proxyservern som anges här kommer att användas i Snapshot Uploader. |

## <a name="cosmosdb"></a>kosmosDb

Konfigurationsinställningen finns i [Cosmos DB-utlösare och bindningar](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>durableTask

Konfigurationsinställningen finns i [bindningar för varaktiga funktioner](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Konfigurationsinställningar finns i [utlösare och bindningar i eventhubb](functions-bindings-event-hubs-output.md#host-json). 

## <a name="extensions"></a>Tillägg

Egenskap som returnerar ett objekt som innehåller alla bindningsspecifika inställningar, till exempel [http](#http) och [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Med tilläggspaket kan du lägga till en kompatibel uppsättning funktionsbindningstillägg i funktionsappen. Mer information finns i [Tilläggspaket för lokal utveckling](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>Funktioner

En lista över funktioner som jobbvärden kör. En tom matris innebär att köra alla funktioner. Avsedd endast för användning när [du kör lokalt](functions-run-local.md). I funktionsappar i Azure bör du i stället följa stegen i Så här inaktiverar du [funktioner i Azure-funktioner](disable-function.md) för att inaktivera specifika funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funktionTimeout

Anger tidsgränsen för alla funktioner. Den följer tidsspannet strängformat. I en serverlös förbrukningsplan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter.  

I Premium-planen är det giltiga intervallet från 1 sekund till 60 minuter och standardvärdet är 30 minuter.

I en dedikerad plan (App Service) finns det ingen total gräns och standardvärdet är 30 minuter. Ett värde `-1` för anger obegränsad körning, men det rekommenderas att behålla en fast övre gräns.

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

Konfigurationsinställningar finns i [http-utlösare och bindningar](functions-bindings-http-webhook-output.md#hostjson-settings).

## <a name="logging"></a>loggning

Styr loggningsbeteendena för funktionsappen, inklusive Application Insights.

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|filLoggingMode|felsökaOnt|Definierar vilken nivå av filloggning som är aktiverad.  Alternativen `never`är `always` `debugOnly`, , . |
|logLevel|Saknas|Objekt som definierar loggkategorifiltrering för funktioner i appen. Version 2.x och senare följer ASP.NET Core-layouten för loggkategorifiltrering. Med den här inställningen kan du filtrera loggning för specifika funktioner. Mer information finns i [Logga filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) i dokumentationen ASP.NET Core. |
|konsol|Saknas| Inställningen [för konsolloggning.](#console) |
|programInsikter|Saknas| Inställningen [applicationInsights.](#applicationinsights) |

## <a name="console"></a>konsol

Den här inställningen är underordnad [loggning](#logging). Den styr konsolloggningen när den inte är i felsökningsläge.

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|ärEnabled|false|Aktiverar eller inaktiverar konsolloggning.| 

## <a name="manageddependency"></a>managedDependency

Hanterat beroende är en funktion som för närvarande endast stöds med PowerShell-baserade funktioner. Det gör att beroenden kan hanteras automatiskt av tjänsten. När `enabled` egenskapen är `true`inställd `requirements.psd1` på bearbetas filen. Beroenden uppdateras när alla delversioner släpps. Mer information finns i [Hanterat beroende](functions-reference-powershell.md#dependency-management) i PowerShell-artikeln.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Köer

Konfigurationsinställningar finns i [Utlösare och bindningar i lagringskö](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid (sendGrid)

Konfigurationsinställningen finns i [SendGrid-utlösare och bindningar](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Konfigurationsinställningen finns i [Service Bus-utlösare och bindningar](functions-bindings-service-bus-output.md#host-json).

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

## <a name="version"></a>version

Det här värdet anger schemaversionen av host.json. Versionssträngen `"version": "2.0"` krävs för en funktionsapp som är inriktad på v2-körningen eller en senare version. Det finns inga host.json-schemaändringar mellan v2 och v3.

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
