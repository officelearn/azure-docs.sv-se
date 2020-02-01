---
title: Strömma Azure-diagnostik data till Event Hubs
description: Konfigurera Azure-diagnostik med Event Hubs slut punkt till slut punkt, inklusive vägledning för vanliga scenarier.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 111fab880887b54b2415d433bda2368c951381bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901216"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Strömma Azure-diagnostik data i den aktiva sökvägen med Event Hubs
Azure-diagnostik ger flexibla sätt att samla in mått och loggar från virtuella datorer i moln tjänster och överföra resultat till Azure Storage. Med början i tids ramen 2016 mars (SDK 2,9) kan du skicka diagnostik till anpassade data källor och överföra frekventa Sök vägs data på några sekunder med hjälp av [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Data typer som stöds är:

* ETW-händelser (Event Tracing for Windows)
* Prestandaräknare
* Händelse loggar i Windows, inklusive program loggar i Windows-händelseloggen
* Azure Diagnostics-infrastrukturloggar

Den här artikeln visar hur du konfigurerar Azure-diagnostik med Event Hubs från slut punkt till slut punkt. Vägledning finns också för följande vanliga scenarier:

* Så här anpassar du de loggar och mått som skickas till Event Hubs
* Ändra konfigurationer i varje miljö
* Visa Event Hubs strömmande data
* Så här felsöker du anslutningen  

## <a name="prerequisites"></a>Krav
Event Hubs att ta emot data från Azure-diagnostik stöds i Cloud Services, virtuella datorer, Virtual Machine Scale Sets och Service Fabric som börjar i Azure SDK 2,9 och motsvarande Azure-verktyg för Visual Studio.

* Azure-diagnostik tillägget 1,6 ([Azure SDK för .net 2,9 eller senare](https://azure.microsoft.com/downloads/) riktar detta som standard)
* [Visual Studio 2013 eller senare](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Befintliga konfigurationer av Azure-diagnostik i ett program med hjälp av en *. wadcfgx* -fil och en av följande metoder:
  * Visual Studio: [Konfigurera diagnostik för Azure Cloud Services och Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows PowerShell: [Aktivera diagnostik i Azure Cloud Services med PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs namn område som tillhandahålls per artikel, [Kom igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Ansluta Azure-diagnostik till Event Hubs mottagare
Som standard skickar Azure-diagnostik alltid loggar och mått till ett Azure Storage konto. Ett program kan också skicka data till Event Hubs genom att lägga till ett nytt **Sinks** -avsnitt under elementet **PublicConfig** / **WadCfg** i *. wadcfgx* -filen. I Visual Studio lagras *. wadcfgx* -filen i följande sökväg: **Cloud Service Project** > **roles** >  **(rolename)**  > **Diagnostics. wadcfgx** -filen.

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

I det här exemplet är Event Hub-URL: en inställd på det fullständigt kvalificerade namn området för händelsehubben: Event Hubs namnrymd + "/" + namn på händelsehubben.  

Hubb-URL: en för händelsehubben visas i [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) på instrument panelen för Event Hubs.  

**Mottagar** namnet kan anges till en giltig sträng så länge samma värde används konsekvent i hela konfigurations filen.

> [!NOTE]
> Det kan finnas ytterligare mottagare, till exempel *applicationInsights* som kon figurer ATS i det här avsnittet. Azure-diagnostik tillåter att en eller flera handfat definieras om varje Sink också deklareras i avsnittet **PrivateConfig** .  
>
>

Event Hubs Sink måste också deklareras och definieras i avsnittet **PrivateConfig** i *. wadcfgx* -konfigurations filen.

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

`SharedAccessKeyName`-värdet måste matcha en nyckel och princip för signatur för delad åtkomst (SAS) som har definierats i namn området **Event Hubs** . Bläddra till Event Hubs instrument panelen i [Azure Portal](https://portal.azure.com), klicka på fliken **Konfigurera** och konfigurera en namngiven princip (till exempel "SendRule") som har *send* -behörighet. **StorageAccount** har också deklarerats i **PrivateConfig**. Du behöver inte ändra värdena här om de fungerar. I det här exemplet lämnar vi värdena tomma, vilket är ett tecken på att en efterföljande till gång kommer att ange värdena. Till exempel anger miljö konfigurations filen *ServiceConfiguration. Cloud. cscfg* miljö-lämpliga namn och nycklar.  

> [!WARNING]
> Event Hubs SAS-nyckeln lagras som oformaterad text i *. wadcfgx* -filen. Den här nyckeln är ofta incheckad till käll kods kontroll eller är tillgänglig som en till gång i din build-Server, så du bör skydda den efter behov. Vi rekommenderar att du använder en SAS-nyckel här med *endast skicka* behörigheter så att en obehörig användare kan skriva till händelsehubben, men inte lyssna på den eller hantera den.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Konfigurera Azure-diagnostik för att skicka loggar och mått till Event Hubs
Som tidigare nämnts, skickas alla standard-och anpassade diagnostikdata, det vill säga mått och loggar, automatiskt till Azure Storage i de konfigurerade intervallen. Med Event Hubs och ytterligare mottagare kan du ange valfri rot-eller lövnod i hierarkin som ska skickas till händelsehubben. Detta inkluderar ETW-händelser, prestanda räknare, Windows-händelseloggar och program loggar.   

Det är viktigt att tänka på hur många data punkter som faktiskt ska överföras till Event Hubs. Normalt överför utvecklare låg latens med låg latens data som måste förbrukas och tolkas snabbt. System som övervakar varningar eller regler för autoskalning är exempel. En utvecklare kan också konfigurera ett alternativt analys lager eller Sök Arkiv, till exempel Azure Stream Analytics, ElasticSearch, ett anpassat övervaknings system eller ett favorit övervaknings system från andra.

Här följer några exempel på konfigurationer.

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

I ovanstående exempel tillämpas sinken på den överordnade **PerformanceCounters** -noden i-hierarkin, vilket innebär att alla underordnade **PerformanceCounters** skickas till Event Hubs.  

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

I det föregående exemplet tillämpas sinken på endast tre räknare: **begär anden i kö**, **begär Anden avvisade**och **% processor tid**.  

I följande exempel visas hur en utvecklare kan begränsa mängden data som skickas till kritiska mått som används för den här tjänstens hälsa.  

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

I det här exemplet används sinken för loggar och filtreras endast till spårning på fel nivå.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Distribuera och uppdatera ett Cloud Services program och diagnostik-konfiguration
Visual Studio är den enklaste sökvägen till att distribuera program-och Event Hubs Sink-konfigurationen. Om du vill visa och redigera filen öppnar du *. wadcfgx* -filen i Visual Studio, redigerar den och sparar den. Sökvägen är **Cloud Service Project** > **roles** >  **(rolename)**  > **Diagnostics. wadcfgx**.  

Nu är alla distributions-och distributions uppdateringar i Visual Studio, Visual Studio Team system och alla kommandon eller skript som är baserade på MSBuild och använder `/t:publish` Target inkludera *. wadcfgx* i förpacknings processen. Distributioner och uppdateringar distribuerar dessutom filen till Azure med hjälp av lämpligt Azure-diagnostik agent tillägg på dina virtuella datorer.

När du har distribuerat programmet och Azure-diagnostik konfiguration kommer du direkt se aktivitet i instrument panelen för Event Hub. Det betyder att du är redo att gå vidare för att visa data för frekventa sökvägar i den lyssnare-klient eller det analys verktyg du väljer.  

I följande bild visar Event Hubs instrument panelen felfritt sändning av diagnostikdata till händelsehubben startar en stund efter 11 PM. Det är när programmet distribuerades med en uppdaterad *. wadcfgx* -fil och Sink har kon figurer ATS korrekt.

![][0]  

> [!NOTE]
> När du gör uppdateringar i Azure-diagnostik konfigurations filen (. wadcfgx) rekommenderar vi att du skickar uppdateringarna till hela programmet och konfigurationen med hjälp av Visual Studio-publicering eller ett Windows PowerShell-skript.  
>
>

## <a name="view-hot-path-data"></a>Visa data för frekventa sökvägar
Som tidigare nämnts finns det många användnings fall för att lyssna på och bearbeta Event Hubs data. En enkel metod är att skapa ett litet test konsol program för att lyssna på händelsehubben och skriva ut utdataströmmen. 

#### <a name="net-sdk-latest-500-or-latertablatest"></a>[.NET SDK senaste (5.0.0 eller senare)](#tab/latest)
Du kan placera följande kod, som beskrivs i mer detalj i [komma igång med Event Hubs](../../event-hubs/get-started-dotnet-standard-send-v2.md)), i ett konsol program.

```csharp
using System;
using System.Text;
using System.Threading.Tasks;
using Azure.Storage.Blobs;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Processor;
namespace Receiver1204
{
    class Program
    {
        private static readonly string ehubNamespaceConnectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static readonly string eventHubName = "EVENT HUB NAME";
        private static readonly string blobStorageConnectionString = "AZURE STORAGE CONNECTION STRING";
        private static readonly string blobContainerName = "BLOB CONTAINER NAME";

        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClientOptions options = new EventProcessorClientOptions { }
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }

        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));
            return Task.CompletedTask;
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
    }
}
```

#### <a name="net-sdk-legacy-410-or-earliertablegacy"></a>[.NET SDK äldre (4.1.0 eller tidigare)](#tab/legacy)

Du kan placera följande kod, som beskrivs i mer detalj i [komma igång med Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)), i ett konsol program. Observera att konsol programmet måste innehålla [Event processors Host NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). Kom ihåg att ersätta värdena i vinkelparenteser i **huvud** funktionen med värden för dina resurser.   

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
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
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
---

## <a name="troubleshoot-event-hubs-sinks"></a>Felsöka Event Hubs-mottagare
* Event Hub visar inte inkommande eller utgående händelse aktivitet som förväntat.

    Kontrol lera att händelsehubben har kon figurer ATS. All anslutnings information i **PrivateConfig** -avsnittet i *. wadcfgx* måste matcha värdena i resursen som visas i portalen. Kontrol lera att du har definierat en SAS-princip ("SendRule" i exemplet) i portalen och att *send* -behörighet beviljas.  
* Efter en uppdatering visar händelsehubben inte längre inkommande eller utgående händelse aktivitet.

    Kontrol lera först att händelsehubben och konfigurations informationen är korrekt enligt beskrivningen ovan. Ibland återställs **PrivateConfig** i en distributions uppdatering. Den rekommenderade korrigeringen är att göra alla ändringar i *. wadcfgx* i projektet och sedan skicka en fullständig program uppdatering. Om detta inte är möjligt kontrollerar du att uppdateringen av diagnostiken pushar en fullständig **PrivateConfig** som innehåller SAS-nyckeln.  
* Jag försökte förslaget, och händelsehubben fungerar fortfarande inte.

    Försök att titta i Azure Storages tabellen som innehåller loggar och fel för Azure-diagnostik sig själv: **WADDiagnosticInfrastructureLogsTable**. Ett alternativ är att använda ett verktyg som [Azure Storage Explorer](https://www.storageexplorer.com) för att ansluta till det här lagrings kontot, Visa den här tabellen och lägga till en fråga för timestamp under de senaste 24 timmarna. Du kan använda verktyget för att exportera en. csv-fil och öppna den i ett program, till exempel Microsoft Excel. Excel gör det enkelt att söka efter telefon korts strängar, till exempel **EventHubs**, för att se vilket fel som rapporteras.  

## <a name="next-steps"></a>Nästa steg
• [Läs mer om Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Bilaga: fullständig Azure-diagnostik konfigurations fil (. wadcfgx)-exempel
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

Den kompletterande *ServiceConfiguration. Cloud. cscfg* -filen för det här exemplet ser ut så här.

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

Skyddade inställningar:
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

* [Översikt över Event Hubs](../../event-hubs/event-hubs-about.md)
* [Skapa en Event Hub](../../event-hubs/event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

