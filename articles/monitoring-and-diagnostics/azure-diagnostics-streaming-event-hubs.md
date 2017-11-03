---
title: "Strömmande data i Azure-diagnostik i varm sökvägen med Händelsehubbar | Microsoft Docs"
description: "Konfigurera Azure-diagnostik med Händelsehubbar slutpunkt till slutpunkt, inklusive anvisningar för vanliga scenarier."
services: event-hubs
documentationcenter: na
author: rboucher
manager: carmonm
editor: 
ms.assetid: edeebaac-1c47-4b43-9687-f28e7e1e446a
ms.service: monitoring-and-diagnostics
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: robb
ms.openlocfilehash: 1c05bd6dc4c4d394aa043b9995de9c184e4f14c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Direktuppspelning Azure-diagnostik i varm sökvägen med hjälp av Händelsehubbar
Azure Diagnostics tillhandahåller flexibelt sätt för att samla in mått och loggar från cloud services virtuella maskiner (VMs) och överföra resultaten till Azure Storage. Från och med mars 2016 (SDK 2.9) tidsintervall du skickar diagnostik till anpassade datakällor och överför varm sökväg data i sekunder med hjälp av [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Datatyper som stöds är:

* ETW-händelser (Event Tracing for Windows)
* Prestandaräknare
* Windows-händelseloggar
* Programloggar
* Azure Diagnostics infrastruktur loggar

Den här artikeln visar hur du konfigurerar Azure Diagnostics med Händelsehubbar från slutpunkt till slutpunkt. Vägledning ges också för vanliga scenarier för följande:

* Hur du anpassar loggar och mått som skickas till Händelsehubbar
* Hur du ändrar konfigurationer i varje miljö
* Så här visar du Händelsehubbar dataströmmen data
* Felsökning av anslutningen  

## <a name="prerequisites"></a>Krav
Event Hubs receieving data från Azure-diagnostik stöds i molntjänster, virtuella datorer, virtuella datorer och Service Fabric från och med Azure SDK 2.9 och motsvarande Azure-verktyg för Visual Studio.

* Azure Diagnostics tillägget 1.6 ([Azure SDK för .NET 2.9 eller senare](https://azure.microsoft.com/downloads/) riktar sig mot detta som standard)
* [Visual Studio 2013 eller senare](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Befintliga konfigurationer av Azure-diagnostik i ett program med hjälp av en *.wadcfgx* fil- och en av följande metoder:
  * Visual Studio: [konfigurera diagnostik för Azure-molntjänster och virtuella datorer](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell: [aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Hubbar händelsenamnrymden etablerats per artikel, [Kom igång med Händelsehubbar](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Anslut Azure Diagnostics till Händelsehubbar mottagare
Som standard skickar Azure-diagnostik alltid loggar och mått till ett Azure Storage-konto. Ett program kan också skicka data till Event Hubs genom att lägga till en ny **egenskaperna** avsnittet den **PublicConfig** / **WadCfg** elementet av den *. wadcfgx* fil. I Visual Studio den *.wadcfgx* lagras i följande sökväg: **Molntjänstprojekt** > **roller** > **() RoleName)** > **diagnostics.wadcfgx** fil.

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

I det här exemplet event hub URL anges till händelsehubben fullständiga namnområde: Händelsehubbar namnområde + ”/” + händelsehubbens namn.  

Händelsehubben URL visas i den [Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885) på instrumentpanelen för Händelsehubbar.  

Den **Sink** namn kan anges till en giltig sträng som samma värde används konsekvent i hela konfigurationsfilen.

> [!NOTE]
> Det kan finnas ytterligare sänkor som *applicationInsights* konfigurerats i det här avsnittet. Azure Diagnostics kan en eller flera handfat definieras om varje mottagare deklareras även i den **PrivateConfig** avsnitt.  
>
>

Händelsehubbar sink måste också deklareras och definieras i den **PrivateConfig** avsnitt i den *.wadcfgx* config-fil.

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

Den `SharedAccessKeyName` värdet måste matcha en delad signatur åtkomst (SAS) nyckel och en princip som har definierats i den **Händelsehubbar** namnområde. Bläddra till Händelsehubbar instrumentpanelen i den [Azure-portalen](https://manage.windowsazure.com), klicka på den **konfigurera** fliken och skapa en namngiven princip (till exempel ”SendRule”) som har *skicka* behörigheter. Den **StorageAccount** också har deklarerats i **PrivateConfig**. Det finns inget behov av att ändra här värden om de fungerar. I det här exemplet lämnar vi värdena tomt, vilket är ett tecken som att den underordnade resursen kommer att ange värden. Till exempel den *ServiceConfiguration.Cloud.cscfg* miljö konfigurationsfil anger miljö som är lämpliga namn och nycklar.  

> [!WARNING]
> Event Hubs SAS-nyckeln lagras i klartext i den *.wadcfgx* fil. Ofta den här nyckeln är markerad för källkodskontroll eller är tillgänglig som en tillgång i build-servern så bör du skydda den efter behov. Vi rekommenderar att du använder SAS-nyckeln här med *Skicka bara* behörigheter så att en obehörig användare inte kan skriva till händelsehubben, men lyssna på den eller hantera den.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurera Azure-diagnostik för att skicka loggar och mått till Händelsehubbar
Som diskuterats tidigare alla standard- och anpassade diagnostikdata, det vill säga skickas loggar, mått och automatiskt till Azure Storage i de konfigurerade intervallen. Du kan ange vilken nod som rot eller lövmedlemmar i hierarkin som ska skickas till händelsehubben med Händelsehubbar och eventuella ytterligare mottagare. Detta inkluderar ETW-händelser, prestandaräknare, Windows-händelseloggar och programloggarna.   

Det är viktigt att tänka på hur många datapunkter faktiskt ska överföras till Händelsehubbar. Vanligtvis dataöverföring utvecklare låg latens hot sökvägen som används och tolkas snabbt. System som övervakar aviseringar eller Autoskala regler är exempel. En utvecklare kan också konfigurera en alternativ analysis-butik eller Sök store – till exempel Azure Stream Analytics Elasticsearch, ett anpassat övervakningssystem eller en favorit övervakningssystemet från andra.

Följande är några exempelkonfigurationer.

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

I exemplet ovan sink används för överordnat **PerformanceCounters** nod i hierarkin, vilket innebär att alla underordnade **PerformanceCounters** kommer att skickas till Händelsehubbar.  

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

I det förra exemplet sink tillämpas endast tre räknare: **begäranden i kö**, **begäranden avvisas**, och **% processortid**.  

I följande exempel visas hur en utvecklare kan begränsa mängden skickade data ska vara viktiga mått som används för den här tjänstens hälsa.  

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

I det här exemplet sink tillämpas loggar och filtreras endast att fel nivå spåra.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Distribuera och uppdatera en molntjänster program och diagnostik config
Visual Studio har den enklaste sökvägen för att distribuera program och Händelsehubbar sink-konfiguration. Om du vill visa och redigera filen, öppna den *.wadcfgx* filen i Visual Studio, redigera och spara den. Sökvägen är **Molntjänstprojekt** > **roller** > **(RoleName)** > **diagnostics.wadcfgx**.  

Nu alla och distributionen uppdatera åtgärder i Visual Studio, Visual Studio Team System och alla kommandon eller skript som är baserade på MSBuild och använder den **/t: publicera** mål omfattar den *.wadcfgx* i paketering processen. Dessutom distribuera distributioner och uppdateringar filen till Azure med hjälp av Azure-diagnostik agent filnamnstillägget på dina virtuella datorer.

När du har distribuerat program och konfiguration av Azure-diagnostik visas direkt aktivitet på instrumentpanelen i händelsehubben. Detta anger att du är redo att gå vidare till att visa data hot sökväg i lyssnare klient- eller verktyg som helst.  

I följande bild visar instrumentpanelen Händelsehubbar felfri sändning av diagnostikdata till händelsehubben från någon gång efter kl. Det är då programmet distribuerades med en uppdaterad *.wadcfgx* fil- och sink har konfigurerats korrekt.

![][0]  

> [!NOTE]
> När du gör uppdateringar i Azure-diagnostik konfigurationsfilen (.wadcfgx) rekommenderas att du push-uppdateringarna till hela programmet samt konfigurationen med hjälp av Visual Studio-publicering eller en Windows PowerShell-skript.  
>
>

## <a name="view-hot-path-data"></a>Visa hot sökväg data
Som tidigare diskuterats, finns det många användningsområden för lyssning och bearbetning av data i Händelsehubbar.

En enkel metod är att skapa ett test små konsolprogram lyssna till händelsehubben och skriva ut utdataströmmen. Du kan placera följande kod, som förklaras i detalj i [Kom igång med Händelsehubbar](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)), i ett konsolprogram.  

Observera att konsolprogrammet måste innehålla den [händelse Processor värden NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Kom ihåg att ersätta värdena i hakparenteser i den **Main** funktion med värden för dina resurser.   

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

## <a name="troubleshoot-event-hubs-sinks"></a>Felsöka Händelsehubbar handfat
* Händelsehubben visar inte inkommande eller utgående händelseaktivitet som förväntat.

    Kontrollera att din händelsehubb har etablerats. Alla anslutningsinformation i den **PrivateConfig** avsnitt i *.wadcfgx* måste matcha värden för din resurs som visas i portalen. Se till att du har en SAS princip definierad (”SendRule” i exemplet) i portalen och som *skicka* behörigheten.  
* När en uppdatering visar händelsehubben inte längre inkommande eller utgående händelseaktivitet.

    Kontrollera först att händelseinformation NAV- och konfigurationen är korrekt enligt beskrivningen tidigare. Ibland den **PrivateConfig** återställs i en distributionsuppdatering. Rekommenderad lösning är att alla ändringar av *.wadcfgx* i projektet och sedan push en uppdatering för hela programmet. Om det inte är möjligt, se till att uppdateringen diagnostik skickar en fullständig **PrivateConfig** som innehåller SAS-nyckeln.  
* Jag har försökt förslagen och händelsehubben fortfarande fungerar inte.

    Titta i Azure Storage-tabell som innehåller loggarna och fel för Azure-diagnostik själva: **WADDiagnosticInfrastructureLogsTable**. Ett alternativ är att använda ett verktyg som [Azure Lagringsutforskaren](http://www.storageexplorer.com) visa den här tabellen för att ansluta till det här lagringskontot och lägga till en fråga för tidsstämpel under de senaste 24 timmarna. Du kan använda verktyget för att exportera en CSV-fil och öppna den i ett program, till exempel Microsoft Excel. Excel gör det enkelt att söka efter strängar som anropar-kort, som **EventHubs**, för att se vilka fel rapporteras.  

## <a name="next-steps"></a>Nästa steg
• [Mer information om Händelsehubbar](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Bilaga: Slutföra exempel på Azure-diagnostik en konfigurationsfil (.wadcfgx)
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

Det kompletterande *ServiceConfiguration.Cloud.cscfg* för det här exemplet ser ut ungefär så här.

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

Motsvarande Json-baserade inställningar för virtuella datorer är följande:
```JSON
"settings": {
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


"protectedSettings": {
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

* [Event Hubs-översikt](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](../event-hubs/event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
