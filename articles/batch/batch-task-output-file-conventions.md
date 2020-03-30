---
title: Beständiga utdata till Azure Storage med .NET-filkonventioner bibliotek - Azure Batch
description: Lär dig hur du använder Azure Batch File Conventions-biblioteket för .NET för att spara batch-uppgift & jobbutdata till Azure Storage och visa utdata i Azure-portalen.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 693017e529f2869c16d94c30cdf48ec228df3276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022876"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Beständiga jobb- och uppgiftsdata till Azure Storage med batchfilskonventioner för .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Ett sätt att spara uppgiftsdata är att använda [Azure Batch File Conventions-biblioteket för .NET][nuget_package]. Filkonventioner biblioteket förenklar processen att lagra uppgifter utdata till Azure Storage och hämta den. Du kan använda filkonventionsbiblioteket &mdash; i både uppgifts- och klientkod i uppgiftskod för beständiga filer och i klientkoden för att lista och hämta dem. Aktivitetskoden kan också använda biblioteket för att hämta utdata från uppströmsaktiviteter, till exempel i ett [aktivitetssambandsscenario.](batch-task-dependencies.md)

Om du vill hämta utdatafiler med filkonventionsbiblioteket kan du hitta filerna för ett visst jobb eller en viss uppgift genom att ange dem efter ID och syfte. Du behöver inte känna till filernas namn eller platser. Du kan till exempel använda filkonventionsbiblioteket för att lista alla mellanliggande filer för en viss uppgift, eller hämta en förhandsgranskningsfil för ett visst jobb.

> [!TIP]
> Från och med version 2017-05-01 stöder Batch-tjänst-API:et beständiga utdata till Azure Storage för uppgifter och jobbhanteringsuppgifter som körs på pooler som skapats med konfigurationen för den virtuella datorn. Batch-tjänst-API:et är ett enkelt sätt att bevara utdata inifrån koden som skapar en uppgift och fungerar som ett alternativ till filkonventionerbiblioteket. Du kan ändra batchklientprogrammen så att de kvarstår utan att behöva uppdatera programmet som aktiviteten körs. Mer information finns i [Beständiga uppgiftsdata till Azure Storage med batch-tjänst-API:et](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>När använder jag filkonventionsbiblioteket för att spara uppgiftsutdata?

Azure Batch ger mer än ett sätt att bevara aktivitetsutdata. Filkonventionerna passar bäst för dessa scenarier:

- Du kan enkelt ändra koden för det program som aktiviteten kör för att spara filer med hjälp av filkonventioner biblioteket.
- Du vill strömma data till Azure Storage medan aktiviteten fortfarande körs.
- Du vill spara data från pooler som skapats med antingen molntjänstkonfigurationen eller konfigurationen för den virtuella datorn.
- Klientprogrammet eller andra uppgifter i jobbet måste hitta och hämta uppgiftsutdatafiler efter ID eller avsiktligt.
- Du vill visa aktivitetsutdata i Azure-portalen.

Om ditt scenario skiljer sig från dem som anges ovan kan du behöva överväga en annan metod. Mer information om andra alternativ för beständig aktivitetsutdata finns i [Beständiga jobb- och aktivitetsutdata till Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Vad är standardstandarden Batch File Conventions?

Standarden [Batch-filkonventioner](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) innehåller ett namngivningsschema för målbehållarna och blob-sökvägarna som dina utdatafiler skrivs till. Filer som sparas i Azure Storage och som följer filkonventioner är automatiskt tillgängliga för visning i Azure-portalen. Portalen är medveten om namngivningskonventionen och kan därför visa filer som följer den.

Filkonventioner för .NET namnger automatiskt dina lagringsbehållare och uppgiftsutdatafiler enligt standarden Filkonventioner. Filkonventionsbiblioteket innehåller också metoder för att fråga utdatafiler i Azure Storage enligt jobb-ID, uppgifts-ID eller syfte.

Om du utvecklar med ett annat språk än .NET kan du själv implementera filkonventionerna i din ansökan. Mer information finns i [Implementera standarden batchfilkonventioner](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Länka ett Azure Storage-konto till ditt batchkonto

Om du vill spara utdata till Azure Storage med hjälp av filkonventioner måste du först länka ett Azure Storage-konto till ditt batchkonto. Om du inte redan har gjort det länkar du ett lagringskonto till ditt batchkonto med hjälp av [Azure-portalen:](https://portal.azure.com)

1. Navigera till ditt Batch-konto i Azure Portal.
1. Under **Inställningar**väljer du **Lagringskonto**.
1. Om du inte redan har ett lagringskonto kopplat till ditt batchkonto klickar du på **Lagringskonto (Ingen)**.
1. Välj ett lagringskonto i listan för din prenumeration. För bästa prestanda kan du använda ett Azure Storage-konto som finns i samma region som batchkontot där dina uppgifter körs.

## <a name="persist-output-data"></a>Beständiga utdata

Om du vill spara jobb- och uppgiftsdata med filkonventioner skapar du en behållare i Azure Storage och sparar sedan utdata till behållaren. Använd [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage) i uppgiftskoden för att överföra uppgiftsutdata till behållaren.

Mer information om hur du arbetar med behållare och blobbar i Azure Storage finns i [Komma igång med Azure Blob-lagring med .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alla jobb- och uppgiftsutdata som sparats med filkonventionsbiblioteket lagras i samma behållare. Om ett stort antal uppgifter försöker spara filer samtidigt kan Azure Storage begränsningsgränser tillämpas. Mer information om begränsningsgränser finns i [Checklista för prestanda och skalbarhet för Blob-lagring](../storage/blobs/storage-performance-checklist.md).

### <a name="create-storage-container"></a>Skapa lagringscontainer

Om du vill spara aktivitetsutdata till Azure Storage skapar du först en behållare genom att anropa [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync][net_prepareoutputasync]. Den här tilläggsmetoden tar ett [CloudStorageAccount-objekt][net_cloudstorageaccount] som en parameter. Den skapar en behållare som namnges enligt standarden Filkonventioner, så att dess innehåll kan identifieras av Azure-portalen och de hämtningsmetoder som beskrivs senare i artikeln.

Du placerar vanligtvis koden för att &mdash; skapa en behållare i klientprogrammet det program som skapar dina pooler, jobb och uppgifter.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Lagra utdata för uppgifter

Nu när du har förberett en behållare i Azure Storage kan uppgifter spara utdata till behållaren med hjälp av klassen [TaskOutputStorage][net_taskoutputstorage] som finns i filkonventionsbiblioteket.

Skapa först ett [TaskOutputStorage-objekt][net_taskoutputstorage] i uppgiftskoden och anropa sedan [TaskOutputStorage][net_taskoutputstorage]när uppgiften har slutfört sitt arbete . [SaveAsync-metoden][net_saveasync] för att spara utdata till Azure Storage.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

Parametern `kind` för [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage). [SaveAsync-metoden](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) kategoriserar de beständiga filerna. Det finns fyra fördefinierade [TaskOutputKind-typer:][net_taskoutputkind] `TaskOutput`, `TaskPreview`, `TaskLog`och `TaskIntermediate.` du kan också definiera anpassade kategorier av utdata.

Med de här utdatatyperna kan du ange vilken typ av utdata som ska anges när du senare frågar Batch för beständiga utdata för en viss aktivitet. Med andra ord, när du listar utdata för en aktivitet kan du filtrera listan på en av utdatatyperna. Till exempel "Ge mig *förhandsgranskningsutdata* för aktivitet *109*." Mer information om att lista och hämta utdata visas i Hämta utdata senare i artikeln.

> [!TIP]
> Utdatasnabbet avgör också var i Azure-portalen en viss fil visas: *TaskOutput*-kategoriserade filer visas under **Utdatafiler**för uppgift och *TaskLog-filer* visas under **Aktivitetsloggar**.

### <a name="store-job-outputs"></a>Lagra jobbutdata

Förutom att lagra aktivitetsutdata kan du lagra utdata som är associerade med ett helt jobb. I sammanfogningsuppgiften för ett filmåtergivningsjobb kan du till exempel bevara den helt renderade filmen som ett jobbutdata. När jobbet är slutfört kan klientprogrammet lista och hämta utdata för jobbet och behöver inte fråga de enskilda uppgifterna.

Lagra jobbutdata genom att anropa [JobOutputStorage][net_joboutputstorage]. [SaveAsync-metoden][net_joboutputstorage_saveasync] och ange [JobOutputKind][net_joboutputkind] och filnamn:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Precis som med **typen TaskOutputKind** för aktivitetsutdata använder du typen [JobOutputKind][net_joboutputkind] för att kategorisera ett jobbs beständiga filer. Med den här parametern kan du senare fråga efter (lista) en viss typ av utdata. Typen **JobOutputKind** innehåller både utdata och förhandsgranskningskategorier och stöder att skapa anpassade kategorier.

### <a name="store-task-logs"></a>Lagra uppgiftsloggar

Förutom att en fil sparas till varaktig lagring när en uppgift eller ett jobb är slutfört &mdash; kan du `stdout.txt` `stderr.txt`behöva spara filer som uppdateras under körningen av en uppgiftsloggfiler eller och , till exempel. För detta ändamål tillhandahåller Azure Batch File Conventions-biblioteket [TaskOutputStorage][net_taskoutputstorage]. [Metoden SaveTrackedAsync.][net_savetrackedasync] Med [SaveTrackedAsync][net_savetrackedasync]kan du spåra uppdateringar till en fil på noden (med ett intervall som du anger) och spara dessa uppdateringar till Azure Storage.

I följande kodavsnitt använder vi [SaveTrackedAsync][net_savetrackedasync] `stdout.txt` för att uppdatera i Azure Storage var 15:e sekund under körningen av uppgiften:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

Det kommenterade `Code to process data and produce output file(s)` avsnittet är en platshållare för den kod som uppgiften normalt skulle utföra. Du kan till exempel ha kod som hämtar data från Azure Storage och utför omvandling eller beräkning på den. Den viktiga delen av det här kodavsnittet visar `using` hur du kan radbrytas sådan kod i ett block för att regelbundet uppdatera en fil med [SaveTrackedAsync][net_savetrackedasync].

Nodagenten är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-kontroll-gränssnittet mellan noden och batch-tjänsten. Anropet `Task.Delay` krävs i slutet `using` av det här blocket för att säkerställa att nodagenten har tid att spola innehållet i standard ut till stdout.txt-filen på noden. Utan denna fördröjning är det möjligt att missa de sista sekunderna av utdata. Den här fördröjningen kanske inte krävs för alla filer.

> [!NOTE]
> När du aktiverar filspårning med **SaveTrackedAsync** *sparas* endast tillägg till den spårade filen i Azure Storage. Använd den här metoden endast för att spåra icke-roterande loggfiler eller andra filer som skrivs till med tilläggsåtgärder i slutet av filen.

## <a name="retrieve-output-data"></a>Hämta utdata

När du hämtar din beständiga utdata med azure batch-filkonventioner biblioteket, gör du det på ett uppgifts- och jobbcentrerat sätt. Du kan begära utdata för en viss uppgift eller jobb utan att behöva känna till sökvägen i Azure Storage eller till och med dess filnamn. I stället kan du begära utdatafiler efter uppgift eller jobb-ID.

Följande kodavsnitt itererar genom ett jobbs uppgifter, skriver ut viss information om utdatafilerna för uppgiften och hämtar sedan filerna från Lagring.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Visa utdatafiler i Azure-portalen

Azure-portalen visar uppgiftsutdatafiler och loggar som sparas i ett länkat Azure Storage-konto med hjälp av [standarden Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Du kan själv implementera dessa konventioner på ett språk som du väljer, eller så kan du använda filkonventionsbiblioteket i DINA .NET-program.

Om du vill aktivera visning av utdatafiler i portalen måste du uppfylla följande krav:

1. Länka ett Azure Storage-konto till ditt batchkonto.
1. Följ de fördefinierade namngivningskonventionerna för lagringsbehållare och filer när utdata kvarstår. Du hittar definitionen av dessa konventioner i filkonventioner biblioteket [README][github_file_conventions_readme]. Om du använder [Azure Batch File Conventions-biblioteket][nuget_package] för att spara utdata, sparas dina filer enligt filkonventionerna.

Om du vill visa uppgiftsutdatafiler och loggar i Azure-portalen navigerar du till den uppgift vars utdata du är intresserad av och klickar sedan på **sparade utdatafiler** eller **Sparade loggar**. Den här bilden visar **de sparade utdatafilerna** för uppgiften med ID "007":

![Bladet aktivitetsutdata i Azure-portalen][2]

## <a name="code-sample"></a>Kodexempel

Exempelprojektet [PersistOutputs][github_persistoutputs] är ett av [Azure Batch-kodexemplen][github_samples] på GitHub. Den här Visual Studio-lösningen visar hur du använder Azure Batch File Conventions-biblioteket för att spara aktivitetsutdata till varaktig lagring. Så här kör du exemplet:

1. Öppna projektet i **Visual Studio 2019**.
2. Lägg till dina batch- och **lagringskontouppgifter** i **AccountSettings.settings** i projektet Microsoft.Azure.Batch.Samples.Common.
3. **Bygg** (men kör inte) lösningen. Återställ alla NuGet-paket om du uppmanas att göra det.
4. Använd Azure-portalen för att ladda upp ett [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera `PersistOutputsTask.exe` de och dess beroende sammansättningarna i ZIP-paketet, ange program-ID:t till "PersistOutputsTask" och programpaketversionen till "1.0".
5. **Starta** (kör) projektet **PersistOutputs.**
6. När du uppmanas att välja den persistensteknik som ska användas för att köra exemplet anger du **1** för att köra exemplet med hjälp av filkonventionsbiblioteket för att bevara aktivitetsutdata. 

## <a name="next-steps"></a>Nästa steg

### <a name="get-the-batch-file-conventions-library-for-net"></a>Hämta batchfilsbiblioteket för .NET

Batch-filkonventioner för .NET är tillgängligt på [NuGet][nuget_package]. Biblioteket utökar [CloudJob-][net_cloudjob] och [CloudTask-klasserna][net_cloudtask] med nya metoder. Se även [referensdokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) för filkonventionsbiblioteket.

[Källkoden][github_file_conventions] för filkonventioner biblioteket är tillgänglig på GitHub i Microsoft Azure SDK för .NET-databasen. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Utforska andra metoder för ihållande utdata

- Se [Beständiga jobb- och uppgiftsutdata till Azure Storage](batch-task-output.md) för en översikt över beständiga uppgifts- och jobbdata.
- Se [Beständiga uppgiftsdata till Azure Storage med batch-tjänst-API:et](batch-task-output-files.md) för att lära dig hur du använder batch-tjänst-API:et för att bevara utdata.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Sparade utdatafiler och sparade loggväljare i portalen"
[2]: ./media/batch-task-output/task-output-02.png "Bladet aktivitetsutdata i Azure-portalen"
