---
title: Host. JSON-referens för Azure Functions 2. x
description: Referens dokumentation för Azure Functions Host. JSON-fil med v2-körningsmiljön.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: b9c57378df1510179c5a45b6aa669bab804aca5e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484442"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Host. JSON-referens för Azure Functions 2. x och senare 

> [!div class="op_single_selector" title1="Välj den version av Azure Functions runtime som du använder: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2 +](functions-host-json.md)

*Host. JSON* -metadatafilen innehåller globala konfigurations alternativ som påverkar alla funktioner för en Function-app. I den här artikeln visas de inställningar som är tillgängliga från och med version 2. x i Azure Functions Runtime.  

> [!NOTE]
> Den här artikeln gäller Azure Functions 2. x och senare versioner.  En referens för Host. json i functions 1. x finns i [Host. JSON-referensen för Azure Functions 1. x](functions-host-json-v1.md).

Andra konfigurations alternativ för Function-appar hanteras i dina [app-inställningar](functions-app-settings.md) (för distribuerade appar) eller din [lokala. Settings. JSON](functions-run-local.md#local-settings-file) -fil (för lokal utveckling).

Konfigurationer i Host. JSON som är relaterade till bindningar tillämpas på samma sätt för varje funktion i Function-appen. 

## <a name="sample-hostjson-file"></a>Exempel på Host. JSON-fil

Följande exempel på *Host. JSON* -fil för version 2. x + har alla möjliga alternativ angivna (exklusive alla som endast används för internt bruk).

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

I följande avsnitt i den här artikeln beskrivs varje toppnivå egenskap. Alla är valfria om inget annat anges.

## <a name="aggregator"></a>aggregera

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Den här inställningen är underordnad [loggning](#logging).

Styr alternativ för Application Insights, inklusive [samplings alternativ](./functions-monitoring.md#configure-sampling).

Den fullständiga JSON-strukturen finns i den tidigare [exempel filen Host. JSON](#sample-hostjson-file).

> [!NOTE]
> Logg sampling kan orsaka att vissa körningar inte visas på bladet Application Insights övervakning. Om du vill undvika logg sampling lägger du till `samplingExcludedTypes: "Request"` till `applicationInsights` svärdet.

| Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| samplingSettings | Saknas | Se [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| samplingExcludedTypes | null | En semikolonavgränsad lista med typer som du inte vill ska samplas. Identifierade typer är: beroende, händelse, undantag, sid visningar, begäran, spårning. Alla instanser av de angivna typerna överförs. de typer som inte har angetts är samplade. |
| samplingIncludedTypes | null | En semikolonavgränsad lista med typer som du vill ska samplas om. en tom lista förutsätter alla typer. Typ som anges i `samplingExcludedTypes` åsidosättning-typer som visas här. Identifierade typer är: beroende, händelse, undantag, sid visningar, begäran, spårning. Alla instanser av de angivna typerna överförs. de typer som inte har angetts är samplade. |
| enableLiveMetrics | true | Aktiverar insamling av Live-mått. |
| enableDependencyTracking | true | Aktiverar beroende spårning. |
| enablePerformanceCountersCollection | true | Aktiverar insamling av kudu prestanda räknare. |
| liveMetricsInitializationDelay | 00:00:15 | Endast för internt bruk. |
| httpAutoCollectionOptions | Saknas | Se [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | Saknas | Se [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| isEnabled | true | Aktiverar eller inaktiverar sampling. | 
| maxTelemetryItemsPerSecond | 20 | Mål antalet för telemetri som loggats per sekund på varje server värd. Om din app körs på många värdar kan du minska det här värdet så att det ligger kvar i den övergripande trafik hastigheten. | 
| evaluationInterval | 01:00:00 | Intervallet då den aktuella takten för telemetri utvärderas igen. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta det här intervallet om din telemetri är ansvarig för plötsliga burst-överföring. |
| initialSamplingPercentage| 1.0 | Den första samplings procenten som används i början av samplings processen för att dynamiskt variera procent andelen. Minska inte värdet när du felsöker. |
| samplingPercentageIncreaseTimeout | 00:00:01 | När värdet för samplings procent ändras avgör den här egenskapen hur snart efteråt Application Insights tillåts att öka samplings procenten igen för att samla in mer data. |
| samplingPercentageDecreaseTimeout | 00:00:01 | När värdet för samplings procenten ändras avgör den här egenskapen hur snart efteråt Application Insights tillåts att sänka samplings procenten igen för att samla in mindre data. |
| minSamplingPercentage | 0,1 | När samplings procenten varierar avgör den här egenskapen den lägsta tillåtna samplings procenten. |
| maxSamplingPercentage | 0,1 | När samplings procenten varierar, fastställer den här egenskapen Maximalt antal tillåtna samplings procent. |
| movingAverageRatio | 1.0 | Vid beräkningen av det glidande medelvärdet har vikten tilldelats det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Lägre värden gör algoritmen mindre aktiv till plötsliga ändringar. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Aktiverar eller inaktiverar utökad HTTP-begäran om HTTP-utlösare: inkommande begäran korrelations rubriker, stöd för flera instrument nycklar, HTTP-metod, sökväg och svar. |
| enableW3CDistributedTracing | true | Aktiverar eller inaktiverar stöd för W3C Distributed tracing Protocol (och aktiverar ett äldre korrelations schema). Aktive ras som standard om `enableHttpTriggerExtendedInfoCollection` är sant. Om `enableHttpTriggerExtendedInfoCollection` är falskt gäller den här flaggan endast för utgående begär Anden, inte inkommande begär Anden. |
| enableResponseHeaderInjection | true | Aktiverar eller inaktiverar inmatning av korrelations rubriker med flera komponenter i svar. Genom att aktivera inmatning kan Application Insights konstruera en program karta till när flera Instrumentation-nycklar används. Aktive ras som standard om `enableHttpTriggerExtendedInfoCollection` är sant. Den här inställningen gäller inte om `enableHttpTriggerExtendedInfoCollection` är falskt. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Mer information om ögonblicks bilder finns i [fel sökning av ögonblicks bilder av undantag i .net-appar](/azure/azure-monitor/app/snapshot-debugger) och [Felsöka problem som aktiverar Application Insights Snapshot debugger eller visning av ögonblicks bilder](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Egenskap | Default | Beskrivning |
| --------- | --------- | --------- | 
| agentEndpoint | null | Slut punkten som används för att ansluta till tjänsten Application Insights Snapshot Debugger. Om värdet är null används en standard slut punkt. |
| captureSnapshotMemoryWeight | 0,5 | Vikten som ges till den aktuella processens minnes storlek vid kontroll om det finns tillräckligt med minne för att ta en ögonblicks bild. Det förväntade värdet är ett större än 0-bråk (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Gränsen för antalet misslyckade förfrågningar för att begära ögonblicks bilder innan telemetri-processorn har inaktiverats.|
| handleUntrackedExceptions | true | Aktiverar eller inaktiverar spårning av undantag som inte spåras av Application Insights telemetri. |
| isEnabled | true | Aktiverar eller inaktiverar ögonblicks bild samling | 
| isEnabledInDeveloperMode | false | Aktiverar eller inaktiverar ögonblicks bild samling är aktive rad i utvecklarläge. |
| isEnabledWhenProfiling | true | Aktiverar eller inaktiverar skapande av ögonblicks bilder även om Application Insights Profiler samlar in en detaljerad profilerings session. |
| isExceptionSnappointsEnabled | false | Aktiverar eller inaktiverar filtrering av undantag. |
| isLowPrioritySnapshotUploader | true | Anger om SnapshotUploader-processen ska köras med lägre prioritet. |
| maximumCollectionPlanSize | 50 | Det maximala antalet problem som vi kan spåra när som helst i ett intervall från ett till 9999. |
| maximumSnapshotsRequired | 3 | Maximalt antal ögonblicks bilder som samlats in för ett enda problem, i ett intervall från en till 999. Ett problem kan betraktas som en enskild Throw-instruktion i ditt program. När antalet ögonblicks bilder som har samlats in för ett problem når detta värde samlas inga fler ögonblicks bilder in för problemet tills problem räknare återställs (se `problemCounterResetInterval`) och gränsen för `thresholdForSnapshotting` nås igen. |
| problemCounterResetInterval | 24:00:00 | Hur ofta du kan återställa problem räknarna i ett intervall från en minut till sju dagar. När det här intervallet har nåtts återställs alla problem antal till noll. Befintliga problem som redan har nått tröskelvärdet för ögonblicks bilder, men som ännu inte har genererat antalet ögonblicks bilder i `maximumSnapshotsRequired`, förblir aktiva. |
| provideAnonymousTelemetry | true | Anger om anonym användning och fel telemetri ska skickas till Microsoft. Den här Telemetrin kan användas om du kontaktar Microsoft för att felsöka problem med Snapshot Debugger. Den används också för att övervaka användnings mönster. |
| reconnectInterval | 00:15:00 | Hur ofta ansluter vi till Snapshot Debugger slut punkten igen. Det tillåtna intervallet är en minut till en dag. |
| shadowCopyFolder | null | Anger den mapp som ska användas för skugg kopiering av binärfiler. Om den inte har angetts försöker mapparna som anges av följande miljövariabler i ordning: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Om värdet är true kommer endast en instans av SnapshotUploader att samla in och överföra ögonblicks bilder för flera appar som delar InstrumentationKey. Om värdet är false blir SnapshotUploader unikt för varje (ProcessName, InstrumentationKey) tupel. |
| snapshotInLowPriorityThread | true | Bestämmer om ögonblicks bilder ska bearbetas i en tråd för låg IO-prioritet. Att skapa en ögonblicks bild är en snabb åtgärd, men för att ladda upp en ögonblicks bild till Snapshot Debugger tjänsten måste den först skrivas till disk som en MiniDump. Detta sker i SnapshotUploader-processen. Genom att ange värdet True används låg prioritets-IO för att skriva Minidump, som inte konkurrerar med ditt program för resurser. Genom att ställa in det här värdet på falskt går det snabbare att skapa Minidump på kostnaderna för att sakta ned programmet. |
| snapshotsPerDayLimit | 30 | Maximalt antal ögonblicks bilder som tillåts på en dag (24 timmar). Den här gränsen tillämpas också på Application Insights tjänst sidan. Uppladdningar är begränsade till 50 per dag per program (det vill säga per Instrumentation-nyckel). Det här värdet förhindrar skapandet av ytterligare ögonblicks bilder som slutligen kommer att avvisas under överföringen. Värdet noll tar bort gränsen helt, vilket inte rekommenderas. |
| snapshotsPerTenMinutesLimit | 1 | Maximalt antal ögonblicks bilder som tillåts om 10 minuter. Även om det inte finns något övre gränser för det här värdet, är det bättre att öka det på produktions arbets belastningar eftersom det kan påverka programmets prestanda. Det går snabbt att skapa en ögonblicks bild, men att skapa en MiniDump av ögonblicks bilden och ladda upp den till Snapshot Debugger tjänsten är en mycket långsammare åtgärd som kommer att konkurrera med ditt program för resurser (både CPU och I/O). |
| tempFolder | null | Anger den mapp där loggfiler för minidumpar och uppladdning ska skrivas. Om inget anges används *%Temp%\Dumps* . |
| thresholdForSnapshotting | 1 | Hur många gånger Application Insights måste se ett undantag innan det begär ögonblicks bilder. |
| uploaderProxy | null | Åsidosätter den proxyserver som används vid avhämtning av ögonblicks bilder. Du kan behöva använda den här inställningen om ditt program ansluter till Internet via en proxyserver. Snapshot Collector körs i programmets process och kommer att använda samma proxyinställningar. Men uppladdning av ögonblicks bilder körs som en separat process och du kan behöva konfigurera proxyservern manuellt. Om det här värdet är null försöker Snapshot Collector automatiskt identifiera proxyns adress genom att undersöka system .net. WebRequest. DefaultWebProxy och skicka värdet till överföring av ögonblicks bilder. Om värdet inte är null används inte automatisk identifiering och proxyservern som anges här kommer att användas i överföring av ögonblicks bilder. |

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

## <a name="functions"></a>funktionen

En lista med funktioner som jobb värden kör. En tom matris innebär att köra alla-funktioner. Endast avsedd att användas när du [Kör lokalt](functions-run-local.md). I Function-appar i Azure bör du i stället följa stegen i [så här inaktiverar du funktioner i Azure Functions](disable-function.md) för att inaktivera vissa funktioner i stället för att använda den här inställningen.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Anger varaktigheten för alla funktioner. Det följer sträng formatet TimeSpan. I en server lös förbruknings plan är det giltiga intervallet från 1 sekund till 10 minuter och standardvärdet är 5 minuter.  

I Premium-planen är det giltiga intervallet mellan 1 sekund och 60 minuter och standardvärdet är 30 minuter.

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|enabled|true|Anger om funktionen är aktive rad. | 
|healthCheckInterval|10 sekunder|Tidsintervallet mellan de regelbundna hälso kontrollerna i bakgrunden. | 
|healthCheckWindow|2 minuter|Ett glidande tids fönster som används tillsammans med `healthCheckThreshold`-inställningen.| 
|healthCheckThreshold|6|Maximalt antal gånger som hälso kontrollen kan återställas innan en återkallning av en värd initieras.| 
|counterThreshold|0,80|Tröskelvärdet som en prestanda räknare kommer att anses vara ohälsosam.| 

## <a name="http"></a>http

Konfigurations inställningar kan hittas i [http-utlösare och bindningar](functions-bindings-http-webhook-output.md#hostjson-settings).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definierar vilken nivå av fil loggning som är aktive rad.  Alternativen är `never`, `always``debugOnly`. |
|logLevel|Saknas|Objekt som definierar logg kategori filtrering för funktioner i appen. Version 2. x och senare följer ASP.NET Core layout för filtrering av loggnings kategorier. Med den här inställningen kan du filtrera loggning för vissa funktioner. Mer information finns i [logg filtrering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) i ASP.net Core-dokumentationen. |
|konsol|Saknas| Loggnings inställningen för [konsolen](#console) . |
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

|Egenskap  |Default | Beskrivning |
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

Du hittar konfigurations inställningar i [utlösare och bindningar för lagrings köer](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Det går att hitta konfigurations inställningen i [SendGrid-utlösare och bindningar](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Du hittar konfigurations inställningen i [Service Bus utlösare och bindningar](functions-bindings-service-bus-output.md#host-json).

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------| 
|lockPeriod|00:00:15|Den period som funktions nivå lås utförs för. Lås automatisk förnyelse.| 
|listenerLockPeriod|00:01:00|Den period som lyssnarens lås tas för.| 
|listenerLockRecoveryPollingInterval|00:01:00|Det tidsintervall som används för återställning av lyssnar lås om det inte gick att hämta ett lyssnar lås vid start.| 
|lockAcquisitionTimeout|00:01:00|Den maximala tid som körningen kommer att försöka hämta ett lås.| 
|lockAcquisitionPollingInterval|Saknas|Intervallet mellan lås försök.| 

## <a name="version"></a>version

Det här värdet anger schema versionen av Host. JSON. Versions strängen `"version": "2.0"` krävs för en Function-app som är riktad mot v2-körningen eller en senare version. Det finns inga värden för Host. JSON-schemat mellan v2 och v3.

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
