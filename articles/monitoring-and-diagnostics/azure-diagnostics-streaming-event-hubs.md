---
title: Stream Azure Diagnostics-data till Event Hubs
description: Konfigurera Azure Diagnostics-data med Event Hubs slutpunkt till slutpunkt, inklusive riktlinjer för vanliga scenarier.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: d21b6235c91a7d2f14b8b39c07891efe967ef572
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278250"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Azure Diagnostics-data i den heta sökvägen för direktuppspelning med Event Hubs
Azure-diagnostik tillhandahåller flexibelt sätt att samla in mått och loggar från cloud services-datorer (VM) och överför resultatet till Azure Storage. Från och med mars 2016 (SDK 2.9) tidsram, kan du skickar diagnostik till anpassade datakällor och överföra heta sökvägen data på några sekunder med hjälp av [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Datatyper som stöds är:

* ETW-händelser (Event Tracing for Windows)
* Prestandaräknare
* Windows-händelseloggar
* Programloggar
* Azure Diagnostics-infrastrukturloggar

Den här artikeln visar hur du konfigurerar Azure Diagnostics-data med Event Hubs från slutpunkt till slutpunkt. Du får också information i följande vanliga scenarier:

* Hur du anpassar loggar och mått som skickas till Event Hubs
* Hur du ändrar konfigurationer i varje miljö
* Så här visar du dataströmmar med Event Hubs
* Så här felsöker du anslutningen  

## <a name="prerequisites"></a>Förutsättningar
Event Hubs receieving data från Azure Diagnostics stöds i molntjänster, virtuella datorer, Virtual Machine Scale Sets och Service Fabric från och med Azure SDK 2.9 och motsvarande Azure Tools för Visual Studio.

* Azure Diagnostics-tillägg 1.6 ([Azure SDK för .NET 2.9 eller senare](https://azure.microsoft.com/downloads/) riktar sig mot det som standard)
* [Visual Studio 2013 eller senare](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Befintliga konfigurationer av Azure Diagnostics-data i ett program med hjälp av en *.wadcfgx* fil- och en av följande metoder:
  * Visual Studio: [konfigurera diagnostik för Azure-molntjänster och virtuella datorer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs-namnområde etablerat per artikel, [Kom igång med Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Ansluta Azure Diagnostics-data till Event Hubs-mottagare
Som standard skickar Azure Diagnostics alltid loggar och mått till ett Azure Storage-konto. Ett program kan också skicka data till Event Hubs genom att lägga till en ny **egenskaperna** avsnittet den **PublicConfig** / **WadCfg** elementet i den *. wadcfgx* fil. I Visual Studio i *.wadcfgx* lagras i följande sökväg: **Molntjänstprojekt** > **roller** > **() RoleName)** > **diagnostics.wadcfgx** fil.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

I det här exemplet anges URL: en för event hub till fullständigt kvalificerade namnområdet för event hub: Event Hubs-namnområdet + ”/” + event hub-namn.  

Event hub URL-Adressen visas i den [Azure-portalen](https://go.microsoft.com/fwlink/?LinkID=213885) på instrumentpanelen för Event Hubs.  

Den **mottagare** namnet kan vara samma som en giltig sträng så länge samma värde används konsekvent i hela konfigurationsfilen.

> [!NOTE]
> Det kan finnas ytterligare mottagare, till exempel *applicationInsights* konfigurerats i det här avsnittet. Azure-diagnostik kan en eller flera mottagare definieras om varje mottagare deklareras också i den **PrivateConfig** avsnittet.  
>
>

Event Hubs-mottagare måste också deklarerats och definieras i den **PrivateConfig** delen av den *.wadcfgx* konfigurationsfilen.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

Den `SharedAccessKeyName` värdet måste överensstämma med en signatur för delad åtkomst (SAS)-nyckel och en princip som har definierats i den **Händelsehubbar** namnområde. Bläddra till Event Hubs-instrumentpanelen i den [Azure-portalen](https://portal.azure.com), klickar du på den **konfigurera** fliken och skapa en namngiven princip (till exempel ”SendRule”) som har *skicka* behörigheter. Den **StorageAccount** deklareras också i **PrivateConfig**. Det finns ingen anledning att ändra värden här om de arbetar. I det här exemplet lämna vi värdena tomt, vilket är ett tecken på att en underordnad tillgång kommer att ange värden. Till exempel den *ServiceConfiguration.Cloud.cscfg* miljö konfigurationsfilen anger miljö som är lämpliga namn och nycklar.  

> [!WARNING]
> Event Hubs SAS-nyckeln lagras i klartext i den *.wadcfgx* fil. Ofta den här nyckeln har checkats in för källkodskontroll eller är tillgänglig som en tillgång i build-servern, så att du ska skydda den efter behov. Vi rekommenderar att du använder en SAS-nyckel här med *Skicka endast* behörigheter så att en obehörig användare inte kan skriva till händelsehubb, men lyssna på den eller hantera den.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurera Azure Diagnostics för att skicka loggar och mått till Event Hubs
Enligt beskrivningen tidigare alla standard- och anpassade diagnostikdata, det vill säga skickas mått och loggar, automatiskt till Azure Storage i de konfigurerade intervallen. Du kan ange en rot- eller löv nod i hierarkin som ska skickas till event hub med Event Hubs och eventuella ytterligare mottagare. Detta inkluderar ETW-händelser, prestandaräknare, Windows-händelseloggar och programloggar.   

Det är viktigt att tänka på hur många datapunkter faktiskt ska överföras till Event Hubs. Vanligtvis dataöverföring utvecklare med låg latens frekvent sökväg som används och tolkas snabbt. System som övervakar aviseringar eller regler för automatisk skalning är exempel. Utvecklare kan också konfigurera en annan analysis-store eller Sök store – till exempel Azure Stream Analytics, Elasticsearch, ett anpassat övervakningssystem eller en favorit övervakningssystemet från andra.

Här följer några exempelkonfigurationer.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

I exemplet ovan sink används för överordnat **PerformanceCounters** nod i hierarkin, vilket innebär att alla underordnade **PerformanceCounters** kommer att skickas till Event Hubs.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

I exemplet ovan sink tillämpas endast tre räknare: **begärandena**, **begäranden avvisas**, och **% processortid**.  

I följande exempel visar hur utvecklare kan begränsa mängden skickade data ska vara viktiga mått som används för den här tjänstens hälsa.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

I det här exemplet sink tillämpas loggar och är filtrerad endast för fel på spårningen.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Distribuera och uppdatera en Cloud Services-konfiguration i programmet och diagnostik
Visual Studio har det enklaste sättet för att distribuera det program- och Event Hubs mottagare. Om du vill visa och redigera filen, öppna den *.wadcfgx* i Visual Studio, redigera den och spara den. Sökvägen är **Molntjänstprojekt** > **roller** > **(RoleName)** > **diagnostics.wadcfgx**.  

Nu kan alla och distributionen uppdatera åtgärder i Visual Studio, Visual Studio Team System, och alla kommandon eller skript som är baserade på MSBuild och Använd den **/t: publicera** mål är den *.wadcfgx* paketering pågår. Dessutom distribuera distributioner och uppdateringar filen till Azure med hjälp av Azure Diagnostics-tillägget för lämplig agent på dina virtuella datorer.

När du har distribuerat programmet och Azure Diagnostics-konfiguration visas direkt aktivitet i instrumentpanelen för event hub. Detta anger att du är redo att gå vidare till att visa frekvent sökväg-data i lyssnaren klient- eller verktyg som helst.  

I följande bild visar Event Hubs-instrumentpanelen felfria sändning av diagnostikdata till händelsehubben startar en stund efter 23: 00. Det är då programmet distribuerades med en uppdaterad *.wadcfgx* fil- och mottagaren har konfigurerats korrekt.

![][0]  

> [!NOTE]
> När du gör uppdateringar i Azure Diagnostics-konfigurationsfilen (.wadcfgx) rekommenderar vi att du har överfört uppdateringarna till hela programmet, samt konfigurationen med hjälp av Visual Studio-publicering eller ett Windows PowerShell-skript.  
>
>

## <a name="view-hot-path-data"></a>Visa frekvent sökväg data
Som beskrivs tidigare kan finns det många användningsområden för att lyssna på och bearbeta data i Händelsehubbar.

En enkel metod är att skapa ett litet test-konsolprogram för att lyssna på händelsehubben och skriva ut utdataströmmen. Du kan placera följande kod, vilket förklaras i detalj i [Kom igång med Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), i ett konsolprogram.  

Observera att konsolprogrammet måste innehålla den [Event Processor Host NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Kom ihåg att ersätta värdena i vinkelparenteser i den **Main** funktionen med värden för dina resurser.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Felsöka Händelsehubbar mottagare
* Event hub visar inte inkommande eller utgående evenemang aktivitet som förväntat.

    Kontrollera att din händelsehubb har etablerats. Alla anslutningsinformation i den **PrivateConfig** delen av *.wadcfgx* måste matcha värdena för din resurs som visas i portalen. Se till att du har en princip för Signatur för definierats (”SendRule” i det här exemplet) i portalen och som *skicka* behörigheten.  
* Efter en uppdatering visar event hub inte längre inkommande eller utgående evenemang aktivitet.

    Kontrollera först att event hub och konfiguration information är korrekt enligt beskrivningen ovan. Ibland den **PrivateConfig** återställs i en uppdatering i distributionen. Rekommenderade korrigeringen är att göra alla ändringar i *.wadcfgx* i projektet och sedan push en uppdatering för hela appen. Om det inte är möjligt, se till att uppdateringen diagnostik skickar ett komplett **PrivateConfig** som innehåller SAS-nyckeln.  
* Jag försökte förslagen och event hub fortfarande fungerar inte.

    Testa att titta i Azure Storage-tabell som innehåller loggarna och fel för Azure-diagnostik själva: **WADDiagnosticInfrastructureLogsTable**. Ett alternativ är att använda ett verktyg som [Azure Storage Explorer](http://www.storageexplorer.com) för att ansluta till det här lagringskontot, visa den här tabellen och Lägg till en fråga för tidsstämpel under de senaste 24 timmarna. Du kan använda verktyget för att exportera en CSV-fil och öppna den i ett program, till exempel Microsoft Excel. Excel är det enkelt att söka efter strängar kort, till exempel **EventHubs**, för att se vilka fel rapporteras.  

## <a name="next-steps"></a>Nästa steg
• [Läs mer om Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Tillägg: Slutför Azure Diagnostics-fil (.wadcfgx) Konfigurationsexempel
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Den kompletterande *ServiceConfiguration.Cloud.cscfg* för det här exemplet ser ut som följande.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Motsvarande JSON-inställningar för virtuella datorer är följande:

Offentliga inställningar:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Inställningarna för replikeringsskyddade:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](../event-hubs/event-hubs-about.md)
* [Skapa en Event Hub](../event-hubs/event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
