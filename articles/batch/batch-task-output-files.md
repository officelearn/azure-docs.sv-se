---
title: "Spara jobb- och utdata till Azure Storage med API för Azure Batch-tjänsten | Microsoft Docs"
description: "Lär dig hur du använder API för Batch-tjänsten för att bevara Batch aktivitets- och utdata till Azure Storage."
services: batch
author: tamram
manager: timlt
editor: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.openlocfilehash: 2530b7c20347b9fb58aee4dfe693847cf3911741
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Spara aktivitetsdata till Azure Storage med API för Batch-tjänsten

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Från och med version 2017-05-01, stöder API för Batch-tjänsten bestående av utdata till Azure Storage för aktiviteter och jobb manager aktiviteter som körs på pooler med konfigurationen av virtuella datorn. När du lägger till en aktivitet kan du ange en behållare i Azure Storage som mål för aktivitetens utdata. Batch-tjänsten skriver sedan alla data på behållaren när uppgiften har slutförts.

En fördel med API för Batch-tjänsten för att bevara uppgiftsutdata är att du inte behöver ändra programmet som aktiviteten körs. Du kan i stället kvarstår aktivitetens utdata från inom den kod som skapar uppgiften med några enkla ändringar i ditt klientprogram.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>När använder API för Batch-tjänsten för att bevara uppgiftsutdata?

Azure Batch tillhandahåller mer än ett sätt att bevara uppgiftens utdata. Med API för Batch-tjänsten är en praktisk metod som passar bäst för dessa scenarier:

- Vill du skriva kod för att bevara uppgiftens utdata från inom ditt klientprogram utan att ändra de program som uppgiften körs.
- Vill du spara utdata från Batch-aktiviteter och jobb manager aktiviteter i pooler som har skapats med den virtuella datorkonfigurationen.
- Vill du spara utdata till en Azure Storage-behållare med ett godtyckligt namn.
- Du vill spara utdata till en Azure Storage-behållare med namnet enligt den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Om ditt scenario skiljer sig från de som visas ovan, kan du behöva överväga en annan metod. Till exempel stöder API för Batch-tjänsten för närvarande inte strömmande utdata till Azure Storage när aktiviteten körs. Överväg att använda Batch filen konventioner-bibliotek för .NET för att strömma utdata. För andra språk behöver du implementera din egen lösning. Mer information om andra alternativ för bestående uppgiftsutdata finns [spara projekt- och utdata till Azure Storage](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>Skapa en behållare i Azure Storage

För att bevara uppgiftens utdata till Azure Storage, behöver du skapa en behållare som fungerar som mål för utgående filer. Skapa behållaren innan du kör uppgiften, helst innan du skickar ditt jobb. Använd lämplig Azure Storage-klientbibliotek eller SDK för att skapa behållaren. Mer information om Azure Storage-API: er finns i [Azure Storage-dokumentationen](https://docs.microsoft.com/azure/storage/).

Till exempel om du skapar ditt program i C#, använda den [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). I följande exempel visas hur du skapar en behållare:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Hämta en signatur för delad åtkomst för behållaren

När du har skapat behållaren får du en signatur för delad åtkomst (SAS) med skrivbehörighet till behållaren. En SAS ger delegerad åtkomst till behållaren. SAS beviljar åtkomst med en angiven uppsättning behörigheter och under ett angivet tidsintervall. Batch-tjänsten måste en SAS med skrivbehörighet för att skriva uppgiftens utdata till behållaren. Mer information om SAS finns [använder signaturer för delad åtkomst \(SAS\) i Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

När du får en SAS med hjälp av Azure Storage-API: er returnerar API: N en SAS-token sträng. Den här token strängen innehåller alla parametrar för SAS, inklusive behörigheter och den period under vilken SAS är giltig. Om du vill använda SAS för att få åtkomst till en behållare i Azure Storage, måste du lägga till SAS-token strängen till resurs-URI. Resurs-URI, tillsammans med tillagda SAS-token ger autentiserad åtkomst till Azure Storage.

I följande exempel visar hur du hämtar en lässkyddad SAS-token sträng för behållaren och sedan lägger till SAS till behållaren för URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Ange utdatafilerna för uppgiftsutdata

Skapa en samling för att ange utdatafilerna för en aktivitet, [utdatafil](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objekt, och tilldela den till den [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) egenskapen när du skapar uppgiften. 

.NET följande exempel skapar en uppgift som skriver slumptal till en fil med namnet `output.txt`. I exemplet skapas en utdatafil för `output.txt` som ska skrivas till behållaren. Exemplet skapar också utdatafilerna för alla loggfiler som matchar mönstret för filen `std*.txt` (_t.ex._, `stdout.txt` och `stderr.txt`). Behållarens Webbadress kräver SAS som skapades tidigare för behållaren. Batch-tjänsten använder SAS för att autentisera åtkomst till behållaren: 

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Ange ett mönster för matchning för filen

När du anger en utdatafil, kan du använda den [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) att ange ett fil-mönster för matchning. Filmönstret kan matcha noll filer, en fil eller en uppsättning filer som skapas av uppgiften.

Den **FilePattern** egenskapen stöder standard filesystem jokertecken som `*` (för icke-rekursiva matchar) och `**` (för rekursiv matchar). Till exempel kodexemplet ovan anger filen mönstret `std*.txt` icke-rekursivt: 

`filePattern: @"..\std*.txt"`

Ange ett fil-mönster med jokertecken för att ladda upp en fil. Till exempel kodexemplet ovan anger filen mönstret `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Ange ett villkor för överföring

Den [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) egenskapen tillåter villkorlig överföra utdatafilerna. Ett vanligt scenario är att lägga upp en uppsättning filer om aktiviteten lyckas och en annan uppsättning filer om den misslyckas. Du kanske vill ladda upp utförlig loggfiler när uppgiften misslyckas och avslutas med slutkoden inte är noll. På samma sätt kan du vill överföra filer resultatet bara om aktiviteten lyckas, eftersom dessa filer kanske saknas eller är ofullständig om aktiviteten misslyckas.

Kodexemplen ovan anger den **UploadCondition** egenskapen **TaskCompletion**. Den här inställningen anger att filen ska överföras när aktiviteterna har slutförts, oavsett värde för slutkoden. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Andra inställningar finns i [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Undvika tvetydigheten filer med samma namn

Aktiviteter i ett projekt kan ge filer som har samma namn. Till exempel `stdout.txt` och `stderr.txt` skapas för varje aktivitet som körs i ett jobb. Eftersom varje aktivitet körs i sin egen kontext, filerna inte är i konflikt i den noden filsystemet. När du överför filer från flera aktiviteter till en delad behållare, måste du dock att undvika tvetydigheten filer med samma namn.

Den [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) egenskapen anger mål-blob eller virtuell katalog för utdatafilerna. Du kan använda den **sökväg** egenskapen att namnge blob eller virtuella katalogen så att filer med samma namn unikt namn i Azure Storage. Med hjälp av aktivitets-ID i sökvägen är ett bra sätt att kontrollera unika namn och lätt att identifiera filer.

Om den **FilePattern** egenskap är inställd på ett jokerteckenuttryck och alla filer som matchar mönstret som överförs till den virtuella katalogen som anges av den **sökväg** egenskapen. Om behållaren är till exempel `mycontainer`, aktiviteten med ID: T är `mytask`, och filmönstret är `..\std*.txt`, och sedan den absoluta URI: er till utdatafilerna i Azure Storage kommer att likna:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Om den **FilePattern** egenskap är inställd på matcha ett enda namn, vilket innebär att den inte innehåller jokertecken och sedan värdet för den **sökväg** egenskapen anger fullständigt kvalificerade blobbnamnet. Om du tror namngivningskonflikter med en enda fil från flera aktiviteter, inkluderar du namnet på den virtuella katalogen som en del av filnamnet för att undvika tvetydigheten dessa filer. Till exempel den **sökväg** egenskapen aktivitets-ID, ett avgränsningstecken (vanligtvis ett snedstreck) och namnet på filen:

`path: taskId + @"/output.txt"`

Absolut URI: er för utdata för en uppsättning uppgifter ska likna följande:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Mer information om virtuella kataloger i Azure Storage finns [visa blobbar i en behållare](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Diagnostisera överför filfel

Om överföringen utdatafilerna till Azure Storage misslyckas aktiviteten och går sedan till den **slutförd** tillstånd och [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) -egenskapen anges. Granska de **FailureInformation** egenskapen fastställa vilka fel uppstod. Här är till exempel ett fel som uppstår på filöverföringen om behållaren inte kan hittas: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

På varje filöverföringen Batch skriver två loggfiler compute-nod `fileuploadout.txt` och `fileuploaderr.txt`. Du kan undersöka dessa loggfiler för mer information om ett specifikt fel. I fall där filöverföringen aldrig gjordes, till exempel eftersom själva uppgiften inte gick att köra sedan finns loggfilerna inte.

## <a name="diagnose-file-upload-performance"></a>Diagnostisera filen överföringsprestanda

Den `fileuploadout.txt` filen loggar överföringen pågår. Du kan undersöka den här filen om du vill lära dig mer om hur lång tid tar din filöverföringar. Tänk på att det finns många bidragande faktorer att överföra prestanda, inklusive storleken på noden, andra aktiviteter på noden vid tiden för överföringen, om Målbehållaren är i samma region som Batch-pool, hur många noder överförs till stora Ge kontot på samma gång och så vidare.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Använd API för Batch-tjänsten med standarden konventioner för Batch-fil

När du bevara uppgiftens utdata med API för Batch-tjänsten kan namnge din målbehållare och blobbar du själv vill. Du kan också välja att ge dem enligt den [Batch filen konventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Filen konventioner standard avgör målbehållare och blob i Azure Storage för en given utdatafilen baserat på namnen på de projekt- och namn. Om du använder filen konventioner standard för namngivning av filer och utgående filer är tillgängliga för visning i den [Azure-portalen](https://portal.azure.com).

Om du utvecklar i C#, kan du använda de metoder som är inbyggda i den [filen konventioner för Batch-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Det här biblioteket skapas korrekt namngivna behållare och blob sökvägar. Du kan till exempel anropa API för att få rätt namn för den behållaren, baserat på jobbnamnet:

```csharp
string containerName = job.OutputStorageContainerName();
```

Du kan använda den [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metod för att returnera en URL för delad åtkomst signatur (SAS) som används för att skriva till behållaren. Du kan sedan överföra den här SAS för den [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

Om du utvecklar i ett annat språk än C#, behöver du implementera filen konventioner standard själv.

## <a name="code-sample"></a>Kodexempel

Den [PersistOutputs] [ github_persistoutputs] exempelprojektet är en av de [kodexempel för Azure Batch] [ github_samples] på GitHub. Visual Studio-lösningen visar hur du använder Batch-klientbibliotek för .NET för att bevara uppgiftens utdata till beständig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2015 eller senare**.
2. Lägg till grupp och lagring **kontoautentiseringsuppgifter** till **AccountSettings.settings** i Microsoft.Azure.Batch.Samples.Common-projektet.
3. **Skapa** (men inte kör) lösningen. Återställa NuGet-paket om du uppmanas till detta.
4. Använda Azure-portalen för att överföra en [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera den `PersistOutputsTask.exe` och dess beroende sammansättningar i ZIP-paketet, ange program-ID till ”PersistOutputsTask” och programpaketets version ”1.0”.
5. **Starta** (kör) den **PersistOutputs** projekt.
6. När du uppmanas att välja beständiga teknik användas för att köra exemplet genom att ange **2** att köra exemplet med API för Batch-tjänsten för att bevara uppgiftens utdata.
7. Om du vill köra exemplet igen, ange **3** att bevara utdata med API för Batch-tjänsten och att namnge målsökvägen för behållaren och blob enligt filen konventioner standard.

## <a name="next-steps"></a>Nästa steg

- Mer information om bestående uppgiftens utdata med filen konventioner-biblioteket för .NET finns [spara projekt- och data till Azure Storage med Batch filen konventioner biblioteket för .NET att bevara ](batch-task-output-file-conventions.md).
- Information om andra metoder för bestående av utdata i Azure Batch finns [spara projekt- och utdata till Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
