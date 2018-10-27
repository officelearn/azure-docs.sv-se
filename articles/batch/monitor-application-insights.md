---
title: Övervaka Batch med Azure Application Insights | Microsoft Docs
description: Lär dig hur du instrumenterar ett Azure Batch .NET-program med Azure Application Insights-biblioteket.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: fb0760f24b8f384818db8154ffe871d7fd4ce429
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138352"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Övervaka och felsöka ett Azure Batch .NET-program med Application Insights

[Application Insights](../application-insights/app-insights-overview.md) är ett elegant och kraftfullt sätt för utvecklare för att övervaka och felsöka program som distribueras till Azure-tjänster. Använda Application Insights för att övervaka prestandaräknare och undantag samt instrumentera din kod med anpassade mått och spårning. Integrera Application Insights med ett Azure Batch-program kan du få djupare insikter om beteenden och undersöka problem i nära realtid.

Den här artikeln visar hur du lägger till och konfigurera Application Insights-biblioteket i din Azure Batch .NET-lösning och instrumentera din programkod. Den visar även sätt att övervaka program via Azure-portalen och skapa anpassade instrumentpaneler. För Application Insights på andra språk, titta på den [språk, plattformar och integreringar dokumentation](../application-insights/app-insights-platforms.md).

Ett exempel C#-lösning med kod som medföljer den här artikeln är tillgänglig på [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Det här exemplet lägger till Application Insights instrumentation kod till den [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) exempel. Om du inte är bekant med det här exemplet försök att skapa och köra TopNWords först. Detta hjälper dig att förstå en grundläggande Batch-arbetsflöde för bearbetning av en uppsättning indatablobbar parallellt på flera beräkningsnoder. 

> [!TIP]
> Alternativt kan du konfigurera Batch-lösningen för att visa Application Insights-data, till exempel prestandaräknare för virtuella datorer i Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnadsfritt, omfattande funktioner, fristående klientverktyg för att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Se den [lagringsplats för batch-insights](https://github.com/Azure/batch-insights) för snabbsteg för att aktivera Application Insights-data i Batch Explorer. 
>

## <a name="prerequisites"></a>Förutsättningar
* [Visual Studio 2017](https://www.visualstudio.com/vs)

* [Batch-konto och ett länkat storage-konto](batch-account-create-portal.md)

* [Application Insights-resurs](../application-insights/app-insights-create-new-resource.md)
  
   * Använd Azure-portalen för att skapa en Application Insights *resource*. Välj den *Allmänt* **programtyp**.

   * Kopiera den [instrumenteringsnyckeln](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) från portalen. Det krävs senare i den här artikeln.
  
  > [!NOTE]
  > Du kanske [debiteras](https://azure.microsoft.com/pricing/details/application-insights/) för data som lagras i Application Insights. Detta inkluderar diagnostiken och övervakning av data som beskrivs i den här artikeln.
  > 

## <a name="add-application-insights-to-your-project"></a>Lägg till Application Insights i projektet

Den **Microsoft.ApplicationInsights.WindowsServer** NuGet-paketet och dess beroenden som krävs för ditt projekt. Lägg till eller återställa dem till ditt programs projekt. Om du vill installera paketet på `Install-Package` kommando eller NuGet-Pakethanteraren.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referera till Application Insights från ditt .NET-program med hjälp av den **Microsoft.ApplicationInsights** namnområde.

## <a name="instrument-your-code"></a>Instrumentera din kod

Din lösning för att instrumentera din kod, som behövs för att skapa en Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). I det här exemplet läser TelemetryClient konfigurationen från den [ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) fil. Se till att uppdatera ApplicationInsights.config i följande projekt med din Application Insights-instrumenteringsnyckeln: Microsoft.Azure.Batch.Samples.TelemetryStartTask och TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Lägg även till instrumenteringsnyckeln i filen TopNWords.cs.

I exemplet i TopNWords.cs använder följande [instrumentation anrop](../application-insights/app-insights-api-custom-events-metrics.md) från Application Insights-API:
* `TrackMetric()` -Spårar hur länge, i genomsnitt en beräkningsnod tar att ladda ned nödvändiga textfilen.
* `TrackTrace()` -Lägger till felsökning anrop till din kod.
* `TrackEvent()` -Spår intressanta händelser för att samla in.

Det här exemplet utelämnar ändamålsenligt undantagshantering. I stället rapporterar Application Insights automatiskt ett ohanterat undantag, vilket avsevärt förbättrar felsökningen. 

Följande kodavsnitt illustrerar hur du använder dessa metoder.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch telemetri-initierare helper
Om du rapporterar telemetri för en viss server och instans, använder Application Insights Azure VM-roll och VM-namn för standardvärden. I samband med Azure Batch visas i exempel hur du använder poolnamnet och compute nodnamnet i stället. Använd en [telemetriinitieraren](../application-insights/app-insights-api-filtering-sampling.md#add-properties) åsidosätta standardvärdena. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Om du vill aktivera telemetri-initierare omfattar filen ApplicationInsights.config i projektet TopNWordsSample följande:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Uppdatera jobb och aktiviteter med Application Insights-binärfiler

Kontrollera att binärfilerna som placeras på rätt sätt för Application Insights kan köras korrekt på compute-noder. Lägg till nödvändiga binärfiler till din uppgift resurssamling filer så att de ladda ned när uppgiften körs. Följande kodfragment liknar koden i Job.cs.

Skapa först en statisk lista över Application Insights-filer att ladda upp.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Därefter skapa de tillfälliga filer som används av aktiviteten.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

Den `FileToStage` metoden är en hjälpfunktionen i kodexemplet som gör det möjligt att enkelt överföra en fil från en lokal disk till en Azure Storage-blob. Varje fil hämtas till en beräkningsnod senare och refereras till av en uppgift.

Slutligen lägger du till aktiviteter i jobbet och innehåller de nödvändiga Application Insights-binärfilerna.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Visa data i Azure portal

Nu när du har konfigurerat jobb och aktiviteter att använda Application Insights, kör du exemplet jobbet i din pool. Gå till Azure-portalen och öppna Application Insights-resursen som du etablerade. När poolen har etablerats bör du se data flödar in och komma loggas. Resten av den här artikeln vidrör på bara några Application Insights-funktioner, men du kan utforska den fullständiga funktionsuppsättningen.

### <a name="view-live-stream-data"></a>Visa data för liveuppspelning

Om du vill visa spårningsloggar i dina program Insights-resurs, klickar du på **Live Stream**. Följande skärmbild visar hur du visar aktuella data kommer från beräkningsnoderna i poolen, till exempel CPU-användningen per compute-nod.

![Live stream beräkning noddata](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visa spårningsloggar

Om du vill visa spårningsloggar i dina program Insights-resurs, klickar du på **Search**. Den här vyn visar en lista över diagnostikdata som avbildas av Application Insights inklusive spårningar, händelser och undantag. 

Följande skärmbild visar hur en enkel spårning för en aktivitet är inloggad och senare efterfrågas vid felsökning.

![Spåra loggar bild](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visa ohanterade undantag

Följande skärmbilder visar hur undantag från ditt program loggar i Application Insights. I det här fallet inom några sekunder för programmet som utlöste undantaget du öka detaljnivån i ett specifikt undantag och diagnostisera problemet.

![Ohanterade undantag](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Måttet blob-hämtningstid

Anpassade mått är också ett värdefullt verktyg i portalen. Du kan till exempel visa den genomsnittliga tid det tog varje compute-nod för att ladda ned nödvändiga textfilen som den bearbetar.

Skapa ett exempeldiagram:
1. I Application Insights-resursen, klickar du på **Måttutforskaren** > **Lägg till diagram**.
2. Klicka på **redigera** i diagrammet som har lagts till.
2. Uppdatera diagrammet informationen på följande sätt:
   * Ange **diagramtyp** till **Grid**.
   * Ange **aggregering** till **genomsnittlig**.
   * Ange **Gruppera efter** till **NodeId**.
   * I **mått**väljer **anpassade** > **Blob download på några sekunder**.
   * Justera visa **färgpalett** till valfria. 

![BLOB-hämtningstid per nod](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Övervaka kontinuerligt beräkningsnoder

Du kanske har märkt att alla mått, inklusive prestandaräknare, loggas endast när aktiviteterna körs. Detta är användbart eftersom det begränsar mängden data som loggar in Application Insights. Det finns dock fall när du vill alltid övervaka compute-noder. Till exempel körs de bakgrundsjobbet som inte har schemalagts via Batch-tjänsten. Konfigurera i det här fallet en övervakningsprocessen för att köra för resten av compute-nod. 

Ett sätt att uppnå det här beteendet är att starta en process som läser in Application Insights-biblioteket och körs i bakgrunden. I det här exemplet läser in de binära filerna på datorn och ser till att en process som körs på obestämd tid. Konfigurera Application Insights-konfigurationsfilen för den här processen att skapa ytterligare data som du är intresserad av, till exempel prestandaräknare.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> För att öka hanterbarheten för din lösning kan du paketera sammansättningen i ett [programpaket](./batch-application-packages.md). För att distribuera programpaketet automatiskt till dina pooler, lägger du till ett program paketreferens till i poolkonfigurationen.
>

## <a name="throttle-and-sample-data"></a>Begränsning och exempeldata 

På grund av storskaliga natur Azure Batch-program som körs i produktionsmiljön, kanske du vill begränsa mängden data som samlas in av Application Insights för att hantera kostnader. Se [Sampling i Application Insights](../application-insights/app-insights-sampling.md) för vissa mekanismer för att åstadkomma detta.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [Application Insights](../application-insights/app-insights-overview.md).

* För Application Insights på andra språk, titta på den [språk, plattformar och integreringar dokumentation](../application-insights/app-insights-platforms.md).


