---
title: Övervaka Batch med Azure Application Insights | Microsoft Docs
description: Lär dig hur du instrumentera ett Azure Batch .NET-program med Azure Application Insights-biblioteket.
services: batch
author: paselem
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: dc2cb7206f5d180008d31bc559f2ce41210e719f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Övervaka och felsöka ett Azure Batch .NET-program med Application Insights

[Application Insights](../application-insights/app-insights-overview.md) är ett elegant och kraftfullt sätt för utvecklare att övervaka och felsöka program som distribueras till Azure-tjänster. Använda Application Insights för att övervaka prestandaräknare och undantag som betalningsinstrument koden med anpassade mått och spårning. Integrering av Application Insights med ditt Azure Batch-program kan du få djupa insikter om beteenden och undersöka problem i nära realtid.

Den här artikeln visar hur du lägger till och konfigurera Application Insights-biblioteket i Azure Batch .NET-lösning och instrumentera programkoden. Här visas också olika sätt att övervaka programmet via Azure portal och skapa anpassade instrumentpaneler. För Application Insights stöd på andra språk, titta på den [språk och plattformar integreringar dokumentationen](../application-insights/app-insights-platforms.md).

Exempel C# med kod som medföljer den här artikeln finns en tillgänglig lösning på [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Det här exemplet lägger till Application Insights instrumentation kod till den [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) exempel. Om du inte är bekant med den exempelvis försök att skapa och köra TopNWords först. Detta hjälper dig att förstå en grundläggande Batch-arbetsflöde för bearbetning av en uppsättning inkommande blobbar parallellt på flera compute-noder. 

## <a name="prerequisites"></a>Förutsättningar
* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015 eller en senare version)

* [Batch-kontot och länkade storage-konto](batch-account-create-portal.md)

* [Application Insights-resurs](../application-insights/app-insights-create-new-resource.md)
  
   * Använda Azure portal för att skapa en Application Insights *resurs*. Välj den *allmänna* **programtyp**.

   * Kopiera den [instrumentation nyckeln](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) från portalen. Det krävs senare i den här artikeln.
  
  > [!NOTE]
  > Du kanske [debiteras](https://azure.microsoft.com/pricing/details/application-insights/) för data som lagras i Application Insights. Detta inkluderar diagnostiken och övervakningsdata som beskrivs i den här artikeln.
  > 

## <a name="add-application-insights-to-your-project"></a>Lägg till Application Insights i projektet

Den **Microsoft.ApplicationInsights.WindowsServer** NuGet-paketet och dess beroenden som krävs för ditt projekt. Lägg till eller återställa dem till ditt program projekt. Installera paketet med den `Install-Package` kommando eller NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referera till Application Insights från .NET-program med hjälp av den **Microsoft.ApplicationInsights** namnområde.

## <a name="instrument-your-code"></a>Instrumentera koden

Om du vill instrumentera koden lösningen behöver för att skapa en Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). I det här exemplet i TelemetryClient läser konfigurationen från den [ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) fil. Se till att uppdatera ApplicationInsights.config i följande projekt med din nyckel för Application Insights instrumentation: Microsoft.Azure.Batch.Samples.TelemetryStartTask och TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Också lägga till instrumentation nyckel i filen TopNWords.cs.

Exemplet i TopNWords.cs använder följande [instrumentation anrop](../application-insights/app-insights-api-custom-events-metrics.md) från Application Insights API:
* `TrackMetric()` -Spårar hur länge, i genomsnitt en beräkningsnod tar för att hämta filen obligatoriska texten.
* `TrackTrace()` -Lägger till felsökning anrop till din kod.
* `TrackEvent()` -Spårar intressanta händelser för att avbilda.

Det här exemplet utelämnar ändamålsenligt undantagshantering. I stället rapporterar Application Insights automatiskt ohanterat undantag, vilket avsevärt förbättrar felsökning upplevelsen. 

Följande utdrag visar hur du använder dessa metoder.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch telemetri initieraren helper
Vid rapportering telemetri för en viss server och instans använder Application Insights Azure VM-roll och VM-namn för standardvärden. I samband med Azure Batch visas i exempel hur du använder namn på pool och beräkna nodnamn i stället. Använd en [telemetri initieraren](../application-insights/app-insights-api-filtering-sampling.md#add-properties) att åsidosätta standardvärden. 

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

Om du vill aktivera telemetri initieraren omfattar ApplicationInsights.config-filen i projektet TopNWordsSample följande:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Uppdatera jobb och uppgifter som ska finnas Application Insights-binärfiler

Kontrollera att de binära filerna placeras på rätt sätt för Application Insights kan köras korrekt på dina compute-noder. Lägg till nödvändiga binärfiler aktivitetens resurssamling filer så att de hämtas när uppgiften körs. Följande kodavsnitt liknar koden i Job.cs.

Först skapa en statisk lista över filer som Application Insights ska överföras.

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

Sedan skapa de tillfälliga filer som används av aktiviteten.
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

Den `FileToStage` metoden är en hjälpfunktion i kodexempel som hjälper dig att enkelt överföra en fil från en lokal disk till en Azure Storage blob. Varje fil hämtas till en beräkningsnod senare och refererar till en aktivitet.

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

## <a name="view-data-in-the-azure-portal"></a>Visa data i Azure-portalen

Nu när du har konfigurerat jobbet och uppgifter för att använda Application Insights kan du köra exemplet jobbet i din pool. Gå till Azure-portalen och öppna Application Insights-resursen som du etablerade. När poolen etableras, bör du börja att se data flödar och komma loggas. Resten av den här artikeln vidrör på bara några Application Insights-funktioner, men gärna att utforska alla funktioner.

### <a name="view-live-stream-data"></a>Visa direktsänd dataström med data

Visa spårningsloggar i ditt program Insights-resurs, klicka på **Liveströmning**. Följande skärmbild visar hur du visar realtidsdata från beräkningsnoder i poolen, till exempel CPU-användning per compute-nod.

![Realtidsdata dataströmmen compute-nod](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visa spårningsloggar

Visa spårningsloggar i ditt program Insights-resurs, klicka på **Sök**. Den här vyn visar en lista över diagnostikdata som avbildas av Application Insights inklusive spårningar, händelser och undantag. 

Följande skärmbild visar hur en enstaka spårning för en aktivitet är inloggad och senare ställa frågor för felsökning.

![Spåra loggar bild](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visa ohanterade undantag

Följande skärmdumpar visar hur Application Insights loggar undantag från ditt program. I det här fallet i sekunder för det program som utlöste undantaget du detaljer om ett specifikt undantag och diagnostisera problemet.

![Ohanterat undantag](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Måttet blob-hämtningstid

Anpassade mått är också ett ovärderligt verktyg i portalen. Du kan till exempel visa den genomsnittliga tid det tog att varje compute-nod för att hämta nödvändiga textfilen som den bearbetade.

Skapa ett exempeldiagram:
1. I Application Insights-resurs klickar du på **Metrics Explorer** > **Lägg till diagram**.
2. Klicka på **redigera** på diagrammet som lades till.
2. Uppdatera informationen diagrammet enligt följande:
   * Ange **diagramtypen** till **rutnätet**.
   * Ange **aggregering** till **genomsnittlig**.
   * Ange **Gruppera efter** till **NodeId**.
   * I **mått**väljer **anpassad** > **Blob nedladdning i sekunder**.
   * Justera visa **färgpalett** till ditt val. 

![BLOB-hämtningstid per nod](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Övervakaren datornoderna kontinuerligt

Kanske såg du alla mått, inklusive prestandaräknare, loggas endast när aktiviteter körs. Detta är användbart eftersom det begränsar mängden data som Application Insights loggar. Det finns dock fall när du vill alltid övervaka compute-noder. Till exempel kanske de körs bakgrundsjobbet som inte har schemalagts via Batch-tjänsten. I det här fallet ställa in en övervakningsprocessen körs för Beräkningsnoden livslängd. 

Ett sätt att uppnå det här problemet är att starta en process som läser in Application Insights-bibliotek och körs i bakgrunden. I det här exemplet Startuppgiften läser in de binära filerna på datorn och för att hålla en process som körs på obestämd tid. Konfigurera Application Insights-konfigurationsfilen för den här processen att generera ytterligare data som du är intresserad av, till exempel prestandaräknare.

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
    virtualMachineSize: "small",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
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
> Om du vill öka hanterbarheten för din lösning kan du paketera sammansättningen i ett [programpaket](./batch-application-packages.md). För att distribuera programpaketet automatiskt till din pooler, lägger du till en programpaketreferens i pool-konfigurationen.
>

## <a name="throttle-and-sample-data"></a>Begränsning av och exempeldata 

På grund av storskaliga hur Azure Batch-program som körs i produktion, kanske du vill begränsa mängden data som samlas in av Application Insights för att hantera kostnader. Se [provtagning i Application Insights](../application-insights/app-insights-sampling.md) för vissa mekanismer för att åstadkomma detta.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Programinsikter](../application-insights/app-insights-overview.md).

* För Application Insights stöd på andra språk, titta på den [språk och plattformar integreringar dokumentationen](../application-insights/app-insights-platforms.md).


