---
title: Beständiga utdata till Azure Storage med Batch-tjänst-API - Azure Batch
description: Lär dig hur du använder batch-tjänst-API:et för att spara batch-aktivitet och jobbutdata till Azure Storage.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 11bd8bc427dd3da35ec5aa0f728f6b04b7d4527d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022859"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Beständiga uppgiftsdata till Azure Storage med batch-tjänst-API:et

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Batch-tjänst-API:et stöder beständiga utdata till Azure Storage för uppgifter och jobbhanterare som körs på pooler med konfigurationen för den virtuella datorn. När du lägger till en uppgift kan du ange en behållare i Azure Storage som mål för aktivitetens utdata. Batch-tjänsten skriver sedan utdata till den behållaren när aktiviteten är klar.

En fördel med att använda batch-tjänst-API:et för att bevara aktivitetsutdata är att du inte behöver ändra programmet som aktiviteten kör. Med några ändringar i klientprogrammet kan du i stället spara aktivitetens utdata inifrån samma kod som skapar uppgiften.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>När använder jag batch-tjänst-API:et för att spara aktivitetsutdata?

Azure Batch ger mer än ett sätt att bevara aktivitetsutdata. Att använda batch-tjänst-API:et är en praktisk metod som passar bäst för dessa scenarier:

- Du vill skriva kod för att bevara aktivitetsutdata inifrån klientprogrammet, utan att ändra programmet som aktiviteten körs.
- Du vill spara utdata från batchuppgifter och jobbhanterare i pooler som skapats med konfigurationen för den virtuella datorn.
- Du vill spara utdata till en Azure Storage-behållare med ett godtyckligt namn.
- Du vill spara utdata till en Azure Storage-behållare med namnet enligt [standarden batchfilkonventioner](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

Om ditt scenario skiljer sig från dem som anges ovan kan du behöva överväga en annan metod. Api:et för batchtjänsten stöder till exempel inte direktuppspelningsutdata till Azure Storage medan aktiviteten körs. Om du vill strömma utdata bör du använda batchfilskonventioner, tillgängligt för .NET. För andra språk måste du implementera din egen lösning. Mer information om andra alternativ för beständig aktivitetsutdata finns i [Beständiga jobb- och aktivitetsutdata till Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Skapa en behållare i Azure Storage

Om du vill spara aktivitetsutdata till Azure Storage måste du skapa en behållare som fungerar som mål för dina utdatafiler. Skapa behållaren innan du kör uppgiften, helst innan du skickar in jobbet. Om du vill skapa behållaren använder du lämpligt Azure Storage-klientbibliotek eller SDK. Mer information om Azure Storage API:er finns i [Azure Storage-dokumentationen](https://docs.microsoft.com/azure/storage/).

Om du till exempel skriver programmet i C#använder du [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). I följande exempel visas hur du skapar en behållare:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Hämta en signatur för delad åtkomst för behållaren

När du har skapat behållaren får du en SAS -signatur (Shared Access signature) med skrivåtkomst till behållaren. En SAS ger delegerad åtkomst till behållaren. SAS beviljar åtkomst med en angiven uppsättning behörigheter och över ett angivet tidsintervall. Batch-tjänsten behöver en SAS med skrivbehörighet för att skriva aktivitetsutdata till behållaren. Mer information om SAS finns i [Använda signaturer \(för delad åtkomst SAS\) i Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

När du får en SAS med Azure Storage API:er returnerar API:et en SAS-tokensträng. Den här tokensträngen innehåller alla parametrar för SAS, inklusive behörigheter och intervall som SAS är giltigt. Om du vill använda SAS för att komma åt en behållare i Azure Storage måste du lägga till SAS-tokensträngen i resursen URI. Resurs-URI:n, tillsammans med den bifogade SAS-token, ger autentiserat åtkomst till Azure Storage.

I följande exempel visas hur du hämtar en SAS-tokensträng med skrivskydd för behållaren och sedan lägger till SAS i behållar-URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Ange utdatafiler för aktivitetsutdata

Om du vill ange utdatafiler för en uppgift skapar du en samling [OutputFile-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) och tilldelar den till egenskapen [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) när du skapar uppgiften.

I följande C#-kodexempel skapas en uppgift `output.txt`som skriver slumptal till en fil med namnet . I exemplet skapas en `output.txt` utdatafil som ska skrivas till behållaren. I exemplet skapas också utdatafiler för alla `std*.txt` loggfiler som matchar `stderr.txt`filmönstret (_t.ex._ `stdout.txt` och ). Behållar-URL:en kräver den SAS som skapades tidigare för behållaren. Batch-tjänsten använder SAS för att autentisera åtkomst till behållaren:

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

### <a name="specify-a-file-pattern-for-matching"></a>Ange ett filmönster för matchning

När du anger en utdatafil kan du använda egenskapen [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) för att ange ett filmönster för matchning. Filmönstret kan matcha noll filer, en enda fil eller en uppsättning filer som skapas av uppgiften.

Egenskapen **FilePattern** stöder jokertecken för `*` filsystem, till exempel (för `**` icke-rekursiva matchningar) och (för rekursiva matchningar). Kodexemplet ovan anger till exempel `std*.txt` filmönstret som ska matcha icke-rekursivt:

`filePattern: @"..\std*.txt"`

Om du vill ladda upp en enskild fil anger du ett filmönster utan jokertecken. Kodexemplet ovan anger till exempel `output.txt`filmönstret som ska matchas:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Ange ett överföringsvillkor

Egenskapen [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tillåter villkorlig uppladdning av utdatafiler. Ett vanligt scenario är att ladda upp en uppsättning filer om uppgiften lyckas och en annan uppsättning filer om den misslyckas. Du kanske till exempel bara vill ladda upp utförliga loggfiler när aktiviteten misslyckas och avslutas med en icke-nollutmatningskod. På samma sätt kanske du bara vill ladda upp resultatfiler om uppgiften lyckas, eftersom dessa filer kan saknas eller vara ofullständiga om aktiviteten misslyckas.

Kodexemplet ovan anger egenskapen **UploadCondition** till **TaskCompletion**. Den här inställningen anger att filen ska överföras när aktiviteterna har slutförts, oavsett värdet för avslutningskoden.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Andra inställningar finns i [uppladdningsuppladdningen För utdatafileUploadCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition)

### <a name="disambiguate-files-with-the-same-name"></a>Disambiguate filer med samma namn

Aktiviteterna i ett jobb kan skapa filer med samma namn. Till exempel `stdout.txt` `stderr.txt` och skapas för varje aktivitet som körs i ett jobb. Eftersom varje aktivitet körs i sin egen kontext står dessa filer inte i konflikt med nodens filsystem. Men när du laddar upp filer från flera uppgifter till en delad behållare måste du disambiguate filer med samma namn.

Egenskapen [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) anger målblobben eller den virtuella katalogen för utdatafiler. Du kan använda egenskapen **Path** för att namnge blobben eller den virtuella katalogen på ett sådant sätt att utdatafiler med samma namn unikt heter i Azure Storage. Att använda uppgifts-ID:t i sökvägen är ett bra sätt att säkerställa unika namn och enkelt identifiera filer.

Om egenskapen **FilePattern** är inställd på ett jokerteckenuttryck överförs alla filer som matchar mönstret till den virtuella katalog som anges av egenskapen **Sökväg.** Om behållaren till `mycontainer`exempel är , `mytask`är uppgifts-ID: t och filmönstret är `..\std*.txt`, kommer de absoluta URI:erna till utdatafilerna i Azure Storage att likna:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Om egenskapen **FilePattern** är inställd på att matcha ett enda filnamn, vilket innebär att den inte innehåller några jokertecken, anger värdet för egenskapen **Path** det fullständigt kvalificerade blobnamnet. Om du räknar med att namnge konflikter med en enda fil från flera uppgifter, inkludera sedan namnet på den virtuella katalogen som en del av filnamnet för att skilja dessa filer. Ange till exempel egenskapen **Sökväg** så att aktivitets-ID: t, avgränsare (vanligtvis ett snedstreck) och filnamnet:

`path: taskId + @"/output.txt"`

De absoluta URI:erna till utdatafilerna för en uppsättning uppgifter liknar:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Mer information om virtuella kataloger i Azure Storage finns [i Lista blobbar i en behållare](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostisera filuppladdningsfel

Om det inte går att överföra utdatafiler till Azure Storage flyttas aktiviteten till tillståndet **Slutförd** och egenskapen [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) har angetts. Undersök egenskapen **FailureInformation** för att ta reda på vilket fel som uppstod. Här är till exempel ett fel som uppstår vid filöverföring om behållaren inte kan hittas:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

På varje filöverföring skriver Batch två loggfiler till `fileuploadout.txt` `fileuploaderr.txt`beräkningsnoden och . Du kan undersöka dessa loggfiler om du vill veta mer om ett specifikt fel. I de fall där filöverföringen aldrig gjordes, till exempel på grund av att själva uppgiften inte kunde köras, kommer dessa loggfiler inte att finnas.

## <a name="diagnose-file-upload-performance"></a>Prestanda för att diagnostisera filuppladdning

Förloppet `fileuploadout.txt` för filloggar överför. Du kan undersöka den här filen om du vill veta mer om hur lång tid filuppladdningarna tar. Tänk på att det finns många bidragande faktorer för att överföra prestanda, inklusive nodens storlek, annan aktivitet på noden vid tidpunkten för överföringen, om målbehållaren finns i samma region som batchpoolen, hur många noder som överförs till lagringskonto samtidigt och så vidare.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Använda batch-tjänst-API:et med standarden Batch-filkonventioner

När du behåller aktivitetsutdata med batch-tjänst-API:et kan du namnge målbehållaren och blobbar hur du vill. Du kan också välja att namnge dem enligt [batchfilkonventioner standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Standarden Filkonventioner bestämmer namnen på målbehållaren och blobben i Azure Storage för en viss utdatafil baserat på namnen på jobbet och uppgiften. Om du använder filkonventioner standard för namngivning av utdatafiler, då dina utdatafiler är tillgängliga för visning i [Azure-portalen](https://portal.azure.com).

Om du utvecklar i C#kan du använda de metoder som är inbyggda i [batchfilkonventioner för .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Det här biblioteket skapar de korrekt namngivna behållarna och blob-sökvägarna åt dig. Du kan till exempel anropa API:et för att få rätt namn för behållaren, baserat på jobbnamnet:

```csharp
string containerName = job.OutputStorageContainerName();
```

Du kan använda metoden [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) för att returnera en SAS-URL (Shared Access Signature) som används för att skriva till behållaren. Du kan sedan skicka denna SAS till [UtdatafileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

Om du utvecklar på ett annat språk än C#, måste du själv implementera filkonventionerna.

## <a name="code-sample"></a>Kodexempel

Exempelprojektet [PersistOutputs][github_persistoutputs] är ett av [Azure Batch-kodexemplen][github_samples] på GitHub. Den här Visual Studio-lösningen visar hur du använder batchklientbiblioteket för .NET för att spara aktivitetsutdata till varaktig lagring. Så här kör du exemplet:

1. Öppna projektet i **Visual Studio 2019**.
2. Lägg till dina batch- och **lagringskontouppgifter** i **AccountSettings.settings** i projektet Microsoft.Azure.Batch.Samples.Common.
3. **Bygg** (men kör inte) lösningen. Återställ alla NuGet-paket om du uppmanas att göra det.
4. Använd Azure-portalen för att ladda upp ett [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera `PersistOutputsTask.exe` de och dess beroende sammansättningarna i ZIP-paketet, ange program-ID:t till "PersistOutputsTask" och programpaketversionen till "1.0".
5. **Starta** (kör) projektet **PersistOutputs.**
6. När du uppmanas att välja den persistensteknik som ska användas för att köra exemplet anger du **2** för att köra exemplet med batch-tjänst-API:et för att bevara aktivitetsutdata.
7. Om du vill kan du köra exemplet igen, ange **3** för att spara utdata med batch-tjänst-API:et och även för att namnge målbehållaren och blob-sökvägen enligt standarden Filkonventioner.

## <a name="next-steps"></a>Nästa steg

- Mer information om beständiga uppgiftsutdata med filkonventionsbiblioteket för .NET finns [i Beständiga jobb- och uppgiftsdata till Azure Storage med batchfilskonventioner för .NET](batch-task-output-file-conventions.md).
- Information om andra metoder för beständiga utdata i Azure Batch finns i [Beständiga jobb- och uppgiftsutdata till Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
