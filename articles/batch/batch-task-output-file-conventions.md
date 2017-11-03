---
title: "Spara jobb- och utdata till Azure Storage med filen konventioner-biblioteket för .NET - Azure Batch | Microsoft Docs"
description: "Lär dig hur du använder filen konventioner för Azure Batch-biblioteket för .NET för att bevara Batch aktivitets- och utdata till Azure Storage och visa beständiga utdata i Azure-portalen."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a9de327c20463469bc91d9720aa17333a36f919e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>Spara jobb- och data till Azure Storage med Batch filen konventioner biblioteket för .NET att bevara 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Ett sätt att spara uppgiftsdata är att använda den [filen konventioner för Azure Batch-biblioteket för .NET][nuget_package]. Filen konventioner biblioteket gör enklare att lagra utdata för aktiviteten till Azure-lagring och hämtning av den. Du kan använda filen konventioner biblioteket i både uppgiften och klienten kod &mdash; i uppgiftskoden för spara filer och i klientkod till listan och hämta dem. Uppgiftskoden kan också använda biblioteket för att hämta utdata från överordnad uppgifter, som i en [uppgift beroenden](batch-task-dependencies.md) scenario. 

Om du vill hämta utdatafilerna till bibliotek för filen konventioner hittar du filerna för ett visst jobb eller en aktivitet genom efter ID och syfte. Behöver du inte känner till namn eller platser för filer. Du kan till exempel använda filen konventioner biblioteket för att lista alla mellanliggande filer för en viss uppgift eller få en preview-fil för ett visst jobb.

> [!TIP]
> Från och med version 2017-05-01, stöder API för Batch-tjänsten bestående av utdata till Azure Storage för aktiviteter och jobb manager aktiviteter som körs på pooler som har skapats med den virtuella datorkonfigurationen. API för Batch-tjänsten gör det enkelt att bevara utdata från inom den kod som skapar en uppgift och fungerar som ett alternativ i filen konventioner-biblioteket. Du kan ändra din Batch klientprogram att bevara utdata utan att behöva uppdatera de program där aktiviteten körs. Mer information finns i [spara aktivitetsdata till Azure Storage med Batch-tjänsten API](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>När använda filen konventioner biblioteket för att bevara uppgiftsutdata?

Azure Batch tillhandahåller mer än ett sätt att bevara uppgiftens utdata. Fil-konventioner fungerar bäst i dessa scenarier:

- Du kan enkelt ändra koden för det program som kör uppgiften för att spara filer med hjälp av filen konventioner-biblioteket.
- Vill du dataströmmen data till Azure Storage medan uppgiften körs.
- Du vill bevara data från poolen som skapats med molnkonfigurationen för tjänsten eller konfigurationen av virtuella datorn.
- Klientprogrammet eller andra uppgifter i jobbet måste leta upp och hämta utdata aktivitetsfiler genom ID eller syfte. 
- Du vill visa uppgiftens utdata i Azure-portalen.

Om ditt scenario skiljer sig från de som visas ovan, kan du behöva överväga en annan metod. Mer information om andra alternativ för bestående uppgiftsutdata finns [spara projekt- och utdata till Azure Storage](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Vad är standard Batch filen konventioner?

Den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) ger ett namngivningsschema för mål-behållare och blob sökvägar som skrivs utdatafiler. Filer sparas i Azure-lagring som följer standarden filen konventioner automatiskt kan visas i Azure-portalen. Portalen så kan visa filer som följer den är medveten om namngivningskonventionen.

Filen konventioner-biblioteket för .NET namn automatiskt din behållare för lagring och uppgiften utdatafilerna enligt de konventioner för filen som är standard. Filen konventioner biblioteket innehåller också metoder för att fråga utdatafilerna i Azure Storage enligt jobb-ID, aktivitets-ID eller syfte.   

Om du utvecklar med ett annat språk än .NET, kan du implementera filen konventioner standard själv i ditt program. Mer information finns i [om det Batch filen konventioner standard](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Koppla ett Azure Storage-konto till Batch-kontot

För att bevara utdata till Azure Storage med hjälp av filen konventioner bibliotek, måste du först koppla ett Azure Storage-konto till Batch-kontot. Om du inte redan har gjort länka ett lagringskonto till Batch-kontot med hjälp av den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt Batch-konto i Azure Portal. 
2. Under **inställningar**väljer **Lagringskonto**.
3. Om du inte redan har ett lagringskonto som är associerade med Batch-kontot, klickar du på **Storage-konto (ingen)**.
4. Välj ett lagringskonto i listan för din prenumeration. För bästa prestanda använder du ett Azure Storage-konto som är i samma region som Batch-kontot var dina aktiviteter körs.

## <a name="persist-output-data"></a>Spara utdata

Skapa en behållare i Azure Storage för att bevara projekt- och utdata med filen konventioner biblioteket och sedan spara resultatet i behållaren. Använd den [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage) i uppgiftskoden att överföra uppgiftsutdata till behållaren. 

Mer information om hur du arbetar med behållare och blobbar i Azure Storage finns [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alla utdata för jobb- och beständiga med filen konventioner biblioteket lagras i samma behållare. Om ett stort antal aktiviteter som försöker spara filer på samma gång [lagring throttling-begränsning](../storage/common/storage-performance-checklist.md#blobs) eventuellt.
> 
> 

### <a name="create-storage-container"></a>Skapa lagringsbehållaren

För att bevara uppgiftens utdata till Azure Storage först skapa en behållare genom att anropa [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Den här tilläggsmetoden tar en [CloudStorageAccount] [ net_cloudstorageaccount] objekt som parameter. Den skapar en behållare med namnet enligt filen konventioner standard, så att innehållet inte är kan identifieras av Azure-portalen och hämtning av metoderna som beskrivs senare i artikeln.

Du vanligtvis placerar kod för att skapa en behållare i ditt klientprogram &mdash; det program som skapar din pooler, jobb och uppgifter.

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

### <a name="store-task-outputs"></a>Lagra utdata för aktiviteten

Nu när du har skapat en behållare i Azure Storage uppgifter kan spara utdata till behållaren med hjälp av den [TaskOutputStorage] [ net_taskoutputstorage] klass hittades i filen konventioner-biblioteket.

I koden aktivitet, först skapa en [TaskOutputStorage] [ net_taskoutputstorage] objekt och sedan anropa när aktiviteten har slutförts sitt arbete, den [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] metod för att spara dess utdata till Azure Storage.

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

Den `kind` parameter för den [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metoden kategoriserar bevarade filer. Det finns fyra fördefinierade [TaskOutputKind] [ net_taskoutputkind] typer: `TaskOutput`, `TaskPreview`, `TaskLog`, och `TaskIntermediate.` du kan också definiera egna kategorier av utdata.

Dessa utdata-typer kan du ange vilken typ av utdata när du senare fråga Batch för beständiga utdata för en viss uppgift. När du listar utdata för en aktivitet med andra ord kan du filtrera listan på något av följande utdata. Till exempel ”ge mig den *preview* utdata för aktiviteten *109*”. Mer om lista och hämta utdata visas i [hämta utdata](#retrieve-output) senare i artikeln.

> [!TIP]
> Vilken utdata avgör också var i Azure-portalen en viss fil visas: *TaskOutput*-kategoriserade filer visas **aktivitet utdatafiler**, och *TaskLog* filer som visas **uppgift loggar**.
> 
> 

### <a name="store-job-outputs"></a>Lagra utdata för jobbet

Du kan lagra utdata som är associerad med ett hela jobb förutom lagra utdata för aktiviteten. I merge-åtgärd för ett jobb för återgivning av film kan du spara fullständigt återgivna film som en jobbutdata. När jobbet har slutförts ditt klientprogram kan visa och hämta utdata för jobbet, och inte behöver fråga enskilda aktiviteter.

Lagra jobbutdata genom att anropa den [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metod, och ange den [JobOutputKind] [ net_joboutputkind] och filnamn:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Med den **TaskOutputKind** typen för aktiviteten utdata, använder du den [JobOutputKind] [ net_joboutputkind] typ att kategorisera ett jobb har sparat filer. Den här parametern kan du senare fråga (lista) en viss typ av utdata. Den **JobOutputKind** typ innehåller både utdata och förhandsgranska kategorier och kan du skapa egna kategorier.

### <a name="store-task-logs"></a>Lagrar uppgiften loggar

Förutom att spara en fil till beständig lagring när en aktivitet eller jobbet har slutförts, du kan behöva spara filer som uppdateras under körningen av en aktivitet &mdash; loggfiler eller `stdout.txt` och `stderr.txt`, till exempel. För detta ändamål biblioteket konventioner för Azure Batch-filen innehåller den [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metod. Med [SaveTrackedAsync][net_savetrackedasync], kan du spåra uppdateringar till en fil på noden (vid ett intervall som du anger) och spara uppdateringarna till Azure Storage.

I följande kodavsnitt vi använda [SaveTrackedAsync] [ net_savetrackedasync] att uppdatera `stdout.txt` i Azure Storage var 15: e sekund under körningen av aktiviteten:

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

Avsnittet kommenterad `Code to process data and produce output file(s)` är en platshållare för den kod som normalt skulle utföra uppgiften. Du kan till exempel ha kod som hämtar data från Azure Storage och utför transformering eller beräkning på den. Viktig del av den här fragment är visar hur du kan anpassa sådan kod i en `using` block att regelbundet uppdatera en fil med [SaveTrackedAsync][net_savetrackedasync].

Nod-agenten är ett program som körs på varje nod i poolen och ger och kommandokontroll gränssnittet mellan noden och Batch-tjänsten. Den `Task.Delay` anrop måste anges i slutet av det här `using` block så att agenten nod har tid att rensa innehållet i standard out till filen stdout.txt på noden. Utan den här fördröjningen är det möjligt att missa senaste några sekunder för utdata. Den här fördröjningen kan inte krävas för alla filer.

> [!NOTE]
> När du aktiverar spårning med filen **SaveTrackedAsync**, endast *lägger till* till spårade fil sparas till Azure Storage. Använd den här metoden för att hålla reda icke rotera loggfiler och andra filer som har skrivits till med tilläggsåtgärder till slutet av filen.
> 
> 

## <a name="retrieve-output-data"></a>Hämta utdata

När du hämtar din beständiga utdata med filen konventioner för Azure Batch-biblioteket, gör du det i en aktivitet till och jobbet-central sätt. Du kan begära utdata för aktiviteten eller jobb utan att behöva känna till dess sökvägen i Azure Storage eller även dess filnamn. Du kan i stället begära utdatafilerna av aktivitet eller jobb-ID.

Följande kodavsnitt går igenom ett jobb uppgifter, skriver ut information om utdatafilerna som för aktiviteten och hämtar sedan dess filer från lagringsplatsen.

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

Azure-portalen visar aktivitet utdatafilerna och loggar som sparas till en länkad Azure Storage-konto med hjälp av den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Du kan också implementera dessa konventioner i på ett språk som du väljer, eller om du kan använda filen konventioner biblioteket i .NET-program.

Om du vill aktivera visningen av dina utdatafiler i portalen måste du uppfylla följande krav:

1. [Koppla ett Azure Storage-konto](#requirement-linked-storage-account) till Batch-kontot.
2. Följa de fördefinierade regler för namngivning av behållare för lagring och filer när beständighet utdata. Du kan hitta definitionen för dessa regler i filen konventioner biblioteket [viktigt][github_file_conventions_readme]. Om du använder den [Azure Batch filen konventioner] [ nuget_package] biblioteket för att spara din utdata, dina filer sparas enligt filen konventioner standard.

Om du vill visa loggar och utgående filer i Azure portal, navigerar du till aktivitet vars utdata som du är intresserad av, klickar sedan på antingen **sparad utdatafilerna** eller **sparade loggarna**. Den här bilden visar den **sparad utdatafilerna** för uppgiften med ID ”007”:

![Resultat av åtgärder bladet i Azure-portalen][2]

## <a name="code-sample"></a>Kodexempel

Den [PersistOutputs] [ github_persistoutputs] exempelprojektet är en av de [kodexempel för Azure Batch] [ github_samples] på GitHub. Visual Studio-lösningen visar hur du använder filen konventioner för Azure Batch-biblioteket för att bevara uppgiftens utdata till beständig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2015 eller senare**.
2. Lägg till grupp och lagring **kontoautentiseringsuppgifter** till **AccountSettings.settings** i Microsoft.Azure.Batch.Samples.Common-projektet.
3. **Skapa** (men inte kör) lösningen. Återställa NuGet-paket om du uppmanas till detta.
4. Använda Azure-portalen för att överföra en [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera den `PersistOutputsTask.exe` och dess beroende sammansättningar i ZIP-paketet, ange program-ID till ”PersistOutputsTask” och programpaketets version ”1.0”.
5. **Starta** (kör) den **PersistOutputs** projekt.
6. När du uppmanas att välja beständiga teknik användas för att köra exemplet genom att ange **1** att köra exemplet med filen konventioner-biblioteket för att bevara uppgiftens utdata. 

## <a name="next-steps"></a>Nästa steg

### <a name="get-the-batch-file-conventions-library-for-net"></a>Hämta filen konventioner för Batch-biblioteket för .NET

Filen konventioner för Batch-biblioteket för .NET är tillgängligt på [NuGet][nuget_package]. Biblioteket utökar den [CloudJob] [ net_cloudjob] och [CloudTask] [ net_cloudtask] klasser med nya metoder. Se även den [refererar dokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) för filen konventioner-biblioteket.

Den [källkod] [ github_file_conventions] för fil-konventioner bibliotek är tillgängligt på GitHub i Microsoft Azure SDK för .NET-databasen. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Utforska andra metoder för bestående av utdata

- Se [spara projekt- och utdata till Azure Storage](batch-task-output.md) för en översikt över bestående aktivitets- och data.
- Se [spara aktivitetsdata till Azure Storage med Batch-tjänsten API](batch-task-output-files.md) information om hur du använder API för Batch-tjänsten för att bevara utdata.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
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
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Sparad utdatafiler och sparade loggarna väljare i portalen"
[2]: ./media/batch-task-output/task-output-02.png "Resultat av åtgärder bladet i Azure-portalen"
