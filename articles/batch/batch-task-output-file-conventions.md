---
title: Bevara jobb- och utdata till Azure Storage med File Conventions-bibliotek för .NET – Azure Batch | Microsoft Docs
description: Lär dig hur du använder Azure Batch File Conventions-bibliotek för .NET för att spara Batch-uppgift och jobbet utdata till Azure Storage och visa beständiga utdata i Azure-portalen.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96f1cb60dbb7cf08224e8566852cf47fe5f0fa1c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203562"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Ett sätt att bevara uppgiftsdata är att använda den [Azure Batch File Conventions-bibliotek för .NET][nuget_package]. File Conventions-bibliotek förenklar processen med att lagra utdata för aktiviteten till Azure Storage och hämta den. Du kan använda File Conventions-bibliotek i både aktivitets- och klienten kod &mdash; i uppgiftskoden för bevara filer och i klientkod lista och hämta dem. Uppgiftskoden kan också använda biblioteket för att hämta utdata från överordnad aktiviteter, till exempel i en [aktivitetsberoenden](batch-task-dependencies.md) scenario.

Du kan hitta filerna för ett visst jobb eller en aktivitet genom att lista dem efter ID och syfte för att hämta utdatafilerna med File Conventions-bibliotek. Du behöver inte känner till namn eller var filerna. Du kan till exempel använda File Conventions-bibliotek för att lista alla mellanliggande filer för en viss uppgift eller hämta en förhandsversion-fil för ett visst jobb.

> [!TIP]
> Från och med version 2017-05-01, stöder API: et för Batch-tjänsten spara utdata till Azure Storage för aktiviteter och job manager-aktiviteter som körs på pooler som skapats med konfigurationen av virtuella datorn. API: et för Batch-tjänsten tillhandahåller ett enkelt sätt att spara utdata från inom den kod som skapar en uppgift och fungerar som ett alternativ till File Conventions-bibliotek. Du kan ändra dina Batch-klientprogram för att spara utdata utan att behöva uppdatera programmet som uppgiften körs. Mer information finns i [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>När ska jag använda File Conventions-bibliotek för att bevara uppgiftsutdata?

Azure Batch får du fler än ett sätt att spara uppgiftsutdata. File Conventions fungerar bäst för dessa scenarier:

- Du kan enkelt ändra koden för det program som uppgiften körs för att bevara filer med hjälp av File Conventions-bibliotek.
- Du vill strömdata till Azure Storage medan uppgiften körs.
- Du kan bevara data från poolen som skapats med molntjänstkonfigurationen eller konfigurationen av virtuella datorn.
- Ditt klientprogram eller andra uppgifter i jobbet behöver leta upp och hämta utdata aktivitetsfiler efter ID eller efter funktion.
- Du vill visa uppgiftens utdata i Azure-portalen.

Om ditt scenario skiljer sig från de som anges ovan, kan du behöva tänka på en annan metod. Mer information om andra alternativ för att bevara uppgiftsutdata finns [Spara jobb- och utdata till Azure Storage](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>Vad är Batch File Conventions standard?

Den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) ger en namngivningsschemat för mål-behållare och blob-sökvägar som din utdatafilerna skrivs. Filer som sparats till Azure Storage som följer standarden File Conventions blir automatiskt tillgängliga för visning i Azure-portalen. Portalen är medveten om namngivningskonventionen och så kan visa filer som följer den.

File Conventions-bibliotek för .NET-automatiskt namn storage-behållare och uppgiften utdatafilerna enligt den File Conventions som är standard. File Conventions-bibliotek innehåller också metoder för att fråga utdatafilerna i Azure Storage enligt jobb-ID, uppgifts-ID eller syfte.

Om du utvecklar med ett annat språk än .NET, kan du implementera File Conventions-standard själv i ditt program. Mer information finns i [följer Batch File Conventions](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Länka ett Azure Storage-konto till ditt Batch-konto

Om du vill spara utdata till Azure Storage med File Conventions-bibliotek, måste du först koppla ett Azure Storage-konto till Batch-kontot. Om du inte redan har gjort länka ett Storage-konto till ditt Batch-konto med hjälp av den [Azure-portalen](https://portal.azure.com):

1. Navigera till ditt Batch-konto i Azure Portal.
1. Under **inställningar**väljer **Lagringskonto**.
1. Om du inte redan har ett lagringskonto som är associerade med ditt Batch-konto, klickar du på **Storage-konto (ingen)**.
1. Välj ett lagringskonto i listan för din prenumeration. För bästa prestanda använder du ett Azure Storage-konto som är i samma region som Batch-konto där dina aktiviteter kör.

## <a name="persist-output-data"></a>Spara utdata

Skapa en behållare i Azure Storage för att bevara jobb- och utdata med File Conventions-bibliotek och spara utdata till behållaren. Använd den [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage) i koden för att ladda upp i uppgiftens utdata till behållaren. 

Mer information om hur du arbetar med behållare och blobbar i Azure Storage finns i [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Alla jobb- och utdata sparas med filen konventioner biblioteket lagras i samma behållare. Om ett stort antal aktiviteter som försöker att spara filerna på samma gång, [storage nätverksbegränsningar](../storage/common/storage-performance-checklist.md#blobs) eventuellt att startas.

### <a name="create-storage-container"></a>Skapa lagringscontainer

För att bevara uppgiftens utdata till Azure Storage, skapar du först en behållare genom att anropa [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Den här metoden för tillägget tar en [CloudStorageAccount] [ net_cloudstorageaccount] objektet som en parameter. Den skapar en behållare med namnet enligt File Conventions-standarden så att innehållet inte är kan identifieras av Azure-portalen och hämtning av metoderna som beskrivs senare i artikeln.

Du placerar vanligtvis koden för att skapa en behållare i ditt klientprogram &mdash; det program som skapar din pooler, jobb och aktiviteter.

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

### <a name="store-task-outputs"></a>Store-aktiviteten matar ut

Nu när du har förberett en behållare i Azure Storage, uppgifter kan spara utdata till behållaren med hjälp av den [TaskOutputStorage] [ net_taskoutputstorage] klass hittades i File Conventions-bibliotek.

I uppgiftskoden först skapa en [TaskOutputStorage] [ net_taskoutputstorage] objekt och sedan när uppgiften har slutfört sitt arbete, anropar den [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] metod för att spara sina utdata till Azure Storage.

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

Den `kind` -parametern för den [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[ SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) metoden kategoriserar bevarade filer. Det finns fyra fördefinierade [TaskOutputKind] [ net_taskoutputkind] typer: `TaskOutput`, `TaskPreview`, `TaskLog`, och `TaskIntermediate.` du kan också definiera anpassade kategorier av utdata.

Dessa typer av utdata kan du ange vilken typ av utdata till när du senare frågor mot Batch för beständiga utdata för en viss uppgift. Med andra ord kan du filtrera listan på en av utdatatyper som när du listar utdata för en aktivitet. Till exempel ”ge mig den *förhandsversion* utdata för aktiviteten *109*”. Mer information om lista och hämta utdata visas i hämta utdata senare i artikeln.

> [!TIP]
> Vilken typ av utdata anger också var i Azure-portalen en viss fil visas: *TaskOutput*-kategoriserade filer visas under **uppgift utdatafiler**, och *TaskLog* filer visas under **uppgift loggar**.

### <a name="store-job-outputs"></a>Store-jobbutdata

Förutom lagrar utdata för aktiviteten kan lagra du utdata som är associerade med en hela jobbet. I merge-åtgärd för ett Renderingsjobb film kan du spara fullständigt återgivna filmen som en jobbutdata. När jobbet har slutförts, ditt klientprogram kan visa och hämta utdata för jobbet, och inte behöver fråga efter enskilda uppgifter.

Store jobbutdata genom att anropa den [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metoden och ange den [JobOutputKind] [ net_joboutputkind] och ett filnamn:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Precis som med de **TaskOutputKind** typen för aktiviteten utdata du använda den [JobOutputKind] [ net_joboutputkind] Skriv för att kategorisera ett jobb är beständiga filer. Den här parametern kan du senare fråga (lista) en viss typ av utdata. Den **JobOutputKind** typ innehåller både utdata och preview kategorier och kan du skapa anpassade kategorier.

### <a name="store-task-logs"></a>Store uppgift loggar

Förutom att spara en fil till beständig lagring när en aktivitet eller jobbet har slutförts, du kan behöva bevara filer som uppdateras under körningen av en uppgift &mdash; loggfiler eller `stdout.txt` och `stderr.txt`, till exempel. För detta ändamål, Azure Batch File Conventions-bibliotek innehåller den [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metod. Med [SaveTrackedAsync][net_savetrackedasync], kan du spåra uppdateringar till en fil på noden (vid ett intervall som du anger) och spara uppdateringarna till Azure Storage.

I följande kodavsnitt, använder vi [SaveTrackedAsync] [ net_savetrackedasync] att uppdatera `stdout.txt` i Azure Storage var 15: e sekund under körning av aktiviteten:

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

Avsnittet kommenterade `Code to process data and produce output file(s)` är en platshållare för den kod som utför uppgiften normalt. Du kanske till exempel kod som hämtar data från Azure Storage och utför transformering eller beräkning på den. Är viktig del av det här kodfragmentet visar hur du kan packa ihop sådan kod i en `using` blockera att regelbundet uppdatera en fil med [SaveTrackedAsync][net_savetrackedasync].

Node-agent är ett program som körs på varje nod i poolen och ger kommando och kontroll över gränssnittet mellan noden och Batch-tjänsten. Den `Task.Delay` krävs i slutet av det här `using` förhindra om du vill kontrollera att noden agenten har tid att rensa innehållet i standard ut till stdout.txt filen på noden. Utan den här fördröjningen är det möjligt att missa senaste några sekunder för utdata. Den här fördröjningen är eventuellt inte krävs för alla filer.

> [!NOTE]
> När du aktiverar spårning med filen **SaveTrackedAsync**, endast *lägger till* till spårade filen sparas till Azure Storage. Använd den här metoden för att hålla reda icke rotera loggfiler och andra filer som har skrivits till med att lägga till åtgärder i slutet av filen.

## <a name="retrieve-output-data"></a>Hämta utdata

När du hämtar dina beständiga utdata med Azure Batch File Conventions-bibliotek kan göra du det på ett sätt som uppgift - och jobbet-centric. Du kan begära utdata för den givna aktivitets- eller utan att behöva veta dess sökväg i Azure Storage eller även dess filnamn. Du kan i stället begära utdatafilerna av aktivitet eller jobb-ID.

Följande kodavsnitt upprepas aktiviteterna i ett jobb, skriver ut information om utdata för aktiviteten och hämtar sedan dess filer från Storage.

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

## <a name="view-output-files-in-the-azure-portal"></a>Visa utdatafilerna i Azure portal

Azure-portalen visar utdata aktivitetsfiler och loggar som har sparats till en länkad Azure Storage-konto med hjälp av den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Du kan också implementera dessa konventioner i de ett språk för ditt val eller om du kan använda File Conventions-bibliotek i .NET-program.

Om du vill aktivera visning av din utdatafilerna i portalen, måste du uppfylla följande krav:

1. Länka ett Azure Storage-konto till Batch-kontot.
1. Följa de fördefinierade regler för namngivning av behållare och filer när spara utdata. Du kan hitta definitionen av dessa konventioner i File Conventions-bibliotek [README][github_file_conventions_readme]. Om du använder den [Azure Batch File Conventions] [ nuget_package] -biblioteket för att spara din utdata, dina filer sparas enligt File Conventions-standarden.

Om du vill visa loggar och uppgiften utdatafilerna i Azure-portalen, navigera till aktivitet vars utdata du är intresserad av, klickar sedan på antingen **sparade utdatafiler** eller **sparade loggar**. Den här bilden visar den **sparade utdatafiler** för uppgiften med ID ”007”:

![Resultat av åtgärder på Azure portal][2]

## <a name="code-sample"></a>Kodexempel

Den [PersistOutputs] [ github_persistoutputs] exempelprojektet är en av de [Azure Batch-kodexempel] [ github_samples] på GitHub. Den här Visual Studio-lösningen visar hur du använder Azure Batch File Conventions-bibliotek för att spara uppgiftsutdata till beständig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2017**.
2. Lägg till ditt Batch- och Storage **kontoautentiseringsuppgifter** till **AccountSettings.settings** i Microsoft.Azure.Batch.Samples.Common-projektet.
3. **Skapa** (men inte kör) lösningen. Återställ NuGet-paket om du tillfrågas.
4. Använda Azure-portalen för att överföra en [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera den `PersistOutputsTask.exe` och dess beroende sammansättningar i ZIP-paketet, Ställ in program-ID till ”PersistOutputsTask” och paketversionen som programmet ska ”1.0”.
5. **Starta** (kör) den **PersistOutputs** projekt.
6. När du uppmanas att välja persistence-teknik för att använda för att köra exemplet, ange **1** att köra exemplet med File Conventions-bibliotek för att bevara uppgiftsutdata. 

## <a name="next-steps"></a>Nästa steg

### <a name="get-the-batch-file-conventions-library-for-net"></a>Hämta Batch File Conventions-bibliotek för .NET

Batch File Conventions-bibliotek för .NET finns på [NuGet][nuget_package]. Biblioteket utökar den [CloudJob] [ net_cloudjob] och [CloudTask] [ net_cloudtask] klasser med nya metoder. Se även de [referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) för File Conventions-bibliotek.

Den [källkod] [ github_file_conventions] för File Conventions-bibliotek är tillgängligt på GitHub i Microsoft Azure SDK för .NET-lagringsplatsen. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Utforska andra metoder för att bevara utdata

- Se [Spara jobb- och utdata till Azure Storage](batch-task-output.md) en översikt över bestående uppgift och jobbet data.
- Se [spara uppgiftsdata till Azure Storage med Batch service API](batch-task-output-files.md) att lära dig hur du använder API: et för Batch-tjänsten för att spara utdata.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
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
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Sparade utdatafiler och sparade loggar väljare i portalen"
[2]: ./media/batch-task-output/task-output-02.png "Resultat av åtgärder på Azure portal"
