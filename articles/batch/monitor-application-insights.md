---
title: Övervaka batch med Azure Application Insights | Microsoft-dokument
description: Lär dig hur du instrumenterar ett Azure Batch .NET-program med hjälp av Azure Application Insights-biblioteket.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: b1f4fb0207d4f659861dbd3fdfd1b2d502409935
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022468"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Övervaka och felsöka ett Azure Batch .NET-program med Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) är ett elegant och kraftfullt sätt för utvecklare att övervaka och felsöka program som distribueras till Azure-tjänster. Använd Application Insights för att övervaka prestandaräknare och undantag samt instrumentera din kod med anpassade mått och spårning. Genom att integrera application insights med ditt Azure Batch-program kan du få djup insikt i beteenden och undersöka problem i nära realtid.

Den här artikeln visar hur du lägger till och konfigurerar application insights-biblioteket i din Azure Batch .NET-lösning och instrumenterar din programkod. Den visar också olika sätt att övervaka ditt program via Azure-portalen och skapa anpassade instrumentpaneler. För Application Insights-stöd på andra språk kan du titta på [dokumentationen för språk, plattformar och integrationer](../azure-monitor/app/platforms.md).

Ett exempel C# lösning med kod som följer med den här artikeln finns på [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). I det här exemplet läggs instrumenteringskoden Application Insights till [i exemplet TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Om du inte är bekant med det exemplet kan du prova att bygga och köra TopNWords först. Om du gör det kan du förstå ett grundläggande batcharbetsflöde för bearbetning av en uppsättning indatablobar parallellt på flera beräkningsnoder. 

> [!TIP]
> Som ett alternativ kan du konfigurera batch-lösningen så att programstatistikdata visas, till exempel prestandaräknare för virtuella datorer i Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnadsfritt, utsålt, fristående klientverktyg som hjälper dig att skapa, felsöka och övervaka Azure Batch-program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Se [batch-insights repo](https://github.com/Azure/batch-insights) för snabba steg för att aktivera Application Insights data i Batch Explorer. 
>

## <a name="prerequisites"></a>Krav
* [Visual Studio 2017 eller senare](https://www.visualstudio.com/vs)

* [Batchkonto och länkat lagringskonto](batch-account-create-portal.md)

* [Application Insights-resurs](../azure-monitor/app/create-new-resource.md )
  
   * Använd Azure-portalen för att skapa en application *insights-resurs*. Välj *General* **typen**Allmänt program .

   * Kopiera [instrumenteringsnyckeln](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) från portalen. Det krävs senare i den här artikeln.
  
  > [!NOTE]
  > Du kan [debiteras](https://azure.microsoft.com/pricing/details/application-insights/) för data som lagras i Application Insights. Detta inkluderar diagnostik- och övervakningsdata som beskrivs i den här artikeln.
  > 

## <a name="add-application-insights-to-your-project"></a>Lägga till programinsikter i projektet

**Packaget Microsoft.ApplicationInsights.WindowsServer** NuGet och dess beroenden krävs för projektet. Lägg till eller återställa dem i programmets projekt. Om du vill installera `Install-Package` paketet använder du kommandot eller NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referera till programstatistik från ditt .NET-program med hjälp av namnområdet **Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrumentera din kod

För att instrumentera din kod måste din lösning skapa en Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). I exemplet läser TelemetryClient in konfigurationen från filen [ApplicationInsights.config.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Var noga med att uppdatera ApplicationInsights.config i följande projekt med instrumentationsnyckeln Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask och TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Lägg också till instrumenteringsnyckeln i TopNWords.cs.

I exemplet i TopNWords.cs används följande [instrumenteringsanrop](../azure-monitor/app/api-custom-events-metrics.md) från API:et för Application Insights:
* `TrackMetric()`- Spårar hur lång tid det tar i genomsnitt en beräkningsnod att hämta den textfil som krävs.
* `TrackTrace()`- Lägger felsökning samtal till din kod.
* `TrackEvent()`- Spårar intressanta händelser att fånga.

I det här exemplet utelämnas undantagshantering avsiktligt. I stället rapporterar Application Insights automatiskt ohanterade undantag, vilket avsevärt förbättrar felsökningsupplevelsen. 

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Initialator för Azure Batch-telemetri
När du rapporterar telemetri för en viss server och instans använder Application Insights Azure VM-rollen och VM-namnet för standardvärdena. I kontexten för Azure Batch visar exemplet hur du använder poolnamnet och beräkningsnodnamnet i stället. Använd en [telemetriinitierare](../azure-monitor/app/api-filtering-sampling.md#add-properties) för att åsidosätta standardvärdena. 

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

Om du vill aktivera telemetriinitieraren innehåller filen ApplicationInsights.config i TopNWordsSample-projektet följande:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Uppdatera jobbet och aktiviteterna så att de innehåller binärfiler för programinsikter

För att Application Insights ska fungera korrekt på dina beräkningsnoder kontrollerar du att binärfilerna är korrekt placerade. Lägg till de binärfiler som krävs i aktivitetens resursfilssamling så att de hämtas när aktiviteten körs. Följande kodavsnitt liknar kod i Job.cs.

Skapa först en statisk lista över Application Insights-filer som ska överföras.

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

Skapa sedan de mellanlagringsfiler som används av uppgiften.
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

Metoden `FileToStage` är en hjälpfunktion i kodexemplet som gör att du enkelt kan överföra en fil från lokal disk till en Azure Storage-blob. Varje fil hämtas senare till en beräkningsnod och refereras av en uppgift.

Lägg slutligen till aktiviteterna i jobbet och inkludera nödvändiga binärfiler för Application Insights.
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

## <a name="view-data-in-the-azure-portal"></a>Visa data i Azure-portalen

Nu när du har konfigurerat jobbet och uppgifterna för att använda Application Insights kör du exempeljobbet i poolen. Navigera till Azure-portalen och öppna application insights-resursen som du har etablerat. När poolen har etablerats bör du börja se data flöda och loggas. Resten av den här artikeln berör endast ett fåtal Application Insights-funktioner, men känn dig fri att utforska hela funktionsuppsättningen.

### <a name="view-live-stream-data"></a>Visa data för livestream

Om du vill visa spårningsloggar i appen Applications Insights klickar du på **Live Stream**. Följande skärmbild visar hur du visar livedata som kommer från beräkningsnoderna i poolen, till exempel CPU-användningen per beräkningsnod.

![Beräkningsdata för livestream](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visa spårningsloggar

Om du vill visa spårningsloggar i appen Applications Insights klickar du på **Sök**. Den här vyn visar en lista över diagnostikdata som samlas in av Application Insights, inklusive spårningar, händelser och undantag. 

Följande skärmbild visar hur en enda spårning för en uppgift loggas och senare efterfrågas för felsökning.

![Bild av spårningsloggar](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visa ohanterade undantag

Följande skärmbilder visar hur Application Insights loggar undantag som genereras från ditt program. I det här fallet, inom några sekunder efter att programmet kastar undantaget, kan du öka detaljnivån och diagnostisera problemet.

![Ohanterade undantag](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mät blob nedladdningstid

Anpassade mått är också ett värdefullt verktyg i portalen. Du kan till exempel visa den genomsnittliga tiden det tog varje beräkningsnod för att hämta den textfil som krävs som den bearbetade.

Så här skapar du ett exempeldiagram:
1. Klicka på Diagram för Att lägga**till** **programinsikter** > i resursen Application Insights .
2. Klicka på **Redigera** i diagrammet som lades till.
2. Uppdatera diagraminformationen enligt följande:
   * Ange **diagramtyp** till **rutnät**.
   * Ange **aggregering** till **Medelvärde**.
   * Ange **Grupp efter** **nodeId**.
   * Välj **Anpassad** > **Blob-hämtning i** **mått**på några sekunder .
   * Justera **visningspaletten Färg** efter eget val. 

![Blob nedladdningstid per nod](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Övervaka beräkningsnoder kontinuerligt

Du kanske har märkt att alla mått, inklusive prestandaräknare, bara loggas när aktiviteterna körs. Det här problemet är användbart eftersom det begränsar mängden data som Application Insights loggar. Det finns dock fall när du alltid vill övervaka beräkningsnoderna. De kan till exempel köra bakgrundsarbete som inte har schemalagts via batch-tjänsten. I det här fallet ställer du in en övervakningsprocess för att köras under beräkningsnodens livslängd. 

Ett sätt att uppnå detta beteende är att skapa en process som läser in Application Insights-biblioteket och körs i bakgrunden. I exemplet läser startuppgiften binärfilerna på datorn och håller en process igång på obestämd tid. Konfigurera konfigurationsfilen application insights för den här processen för att avge ytterligare data som du är intresserad av, till exempel prestandaräknare.

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
> Om du vill öka lösningens hanterbarhet kan du paketera sammansättningen i ett [programpaket](./batch-application-packages.md). Om du sedan vill distribuera programpaketet automatiskt till dina pooler lägger du till en programpaketreferens till poolkonfigurationen.
>

## <a name="throttle-and-sample-data"></a>Gasspjäll- och exempeldata 

På grund av den storskaliga karaktären hos Azure Batch-program som körs i produktion kanske du vill begränsa mängden data som samlas in av Application Insights för att hantera kostnader. Se [Sampling i Application Insights](../azure-monitor/app/sampling.md) för vissa mekanismer för att uppnå detta.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [Application Insights](../azure-monitor/app/app-insights-overview.md).

* För Application Insights-stöd på andra språk kan du titta på [dokumentationen för språk, plattformar och integrationer](../azure-monitor/app/platforms.md).


