---
title: Bevara jobb- och utdata till Azure Storage med Batch-tjänsten API – Azure Batch | Microsoft Docs
description: Lär dig hur du använder API för Batch-tjänsten för att spara Batch-uppgift och jobbet utdata till Azure Storage.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 0aa0be7ae9658259b327014c5678777c963e6cb5
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302824"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Spara aktivitetsdata till Azure Storage med API: et för Batch-tjänsten

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Spara utdata till Azure Storage för aktiviteter och job manager-aktiviteter som körs på pooler med konfigurationen av virtuella datorn har stöd för API: et för Batch-tjänsten. När du lägger till en aktivitet kan du ange en behållare i Azure Storage som mål för aktivitetens utdata. Batch-tjänsten skriver sedan alla data på behållaren när aktiviteten har slutförts.

En fördel med att använda API: et för Batch-tjänsten för att bevara uppgiftsutdata är att du inte behöver ändra det program som aktiviteten körs. I stället kan du behålla aktivitetens utdata från inom samma kod som skapar uppgiften med några ändringar i klientprogrammet.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>När ska jag använda API: et för Batch-tjänsten för att bevara uppgiftsutdata?

Azure Batch får du fler än ett sätt att spara uppgiftsutdata. Med API: et för Batch-tjänsten är en praktisk metod som passar bäst för dessa scenarier:

- Vill du skriva kod för att bevara uppgiftens utdata från inom ditt klientprogram utan att ändra de program som uppgiften körs.
- Vill du spara utdata från Batch-aktiviteterna och job manager-aktiviteter i pooler som skapats med konfigurationen av virtuella datorn.
- Vill du spara utdata till en Azure Storage-behållare med ett godtyckligt namn.
- Du vill spara utdata till en Azure Storage-behållare med namnet enligt den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> API för Batch-tjänsten stöder inte bevara data från aktiviteter som körs i pooler som skapats med molntjänstkonfigurationen. Information om bestående aktivitet utdata från pooler med cloud services-konfigurationen finns i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET för att bevara ](batch-task-output-file-conventions.md).

Om ditt scenario skiljer sig från de som anges ovan, kan du behöva tänka på en annan metod. Till exempel stöder API: et för Batch-tjänsten för närvarande inte strömmande utdata till Azure Storage när aktiviteten körs. Att strömma utdata bör du överväga att använda Batch File Conventions-bibliotek för .NET. För övriga språk behöver du implementera en egen lösning. Mer information om andra alternativ för att bevara uppgiftsutdata finns [Spara jobb- och utdata till Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Skapa en behållare i Azure Storage

För att bevara uppgiftens utdata till Azure Storage, behöver du skapa en behållare som fungerar som mål för din utdatafilerna. Skapa behållaren innan du kör uppgiften, helst innan du skickar jobbet. För att skapa behållaren, använder du lämplig Azure Storage-klientbibliotek eller SDK. Mer information om API: erna för Azure Storage finns i den [dokumentation om Azure Storage](https://docs.microsoft.com/azure/storage/).

Till exempel om du skapar ditt program i C#, använda den [Azure Storage-klientbiblioteket för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). I följande exempel visas hur du skapar en behållare:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Hämta signatur för delad åtkomst för behållaren

När du har skapat behållaren får du en signatur för delad åtkomst (SAS) med skrivåtkomst till behållaren. En SAS ger delegerad åtkomst till behållaren. SAS ger åtkomst med en angiven uppsättning behörigheter och under ett angivet tidsintervall. Batch-tjänsten behöver en SAS med skrivbehörighet för att skriva uppgiftens utdata till behållaren. Mer information om SAS finns i [använda signaturer för delad åtkomst \(SAS\) i Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

När du får en SAS med hjälp av API: er för Azure Storage returnerar API: et en token SAS-sträng. Den här token strängen innehåller alla parametrar för SAS, inklusive behörigheter och intervallet över vilket Signaturen är giltig. Om du vill använda SAS för att få åtkomst till en behållare i Azure Storage, måste du lägga till SAS-token strängen till resurs-URI. Resurs-URI, tillsammans med tillagda SAS-token ger autentiserad åtkomst till Azure Storage.

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

Om du vill ange utdatafilerna för en aktivitet, skapar du en samling med [utdatafil](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objekt och tilldela den till den [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) egenskapen när du skapar uppgiften.

Följande C# kodexempel skapar en uppgift som skriver slumptal till en fil med namnet `output.txt`. I exemplet skapas en utdatafil för `output.txt` som ska skrivas till behållaren. Exemplet skapar också utdatafilerna för alla loggfiler som matchar filmönstret `std*.txt` (_t.ex._, `stdout.txt` och `stderr.txt`). Behållarens Webbadress kräver SAS som skapades tidigare för behållaren. Batch-tjänsten använder SAS för att autentisera åtkomst till behållaren:

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

När du anger en utdatafil, du kan använda den [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) egenskapen att ange ett filmönster för matchning. Filmönstret kanske matchar noll filer, en enda fil eller en uppsättning filer som skapas av uppgiften.

Den **FilePattern** egenskap stöder standard filsystem jokertecken som `*` (för icke-rekursiva matchar) och `**` (för rekursiv matchar). Till exempel kodexemplet ovan anger mönstret som filen ska matcha `std*.txt` icke-rekursivt:

`filePattern: @"..\std*.txt"`

Om du vill överföra en fil, ange ett filmönster med jokertecken. Till exempel kodexemplet ovan anger mönstret som filen ska matcha `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Ange ett villkor för överföring

Den [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) egenskapen tillåter villkorlig uppladdningen av utdatafilerna. Ett vanligt scenario är att lägga upp en uppsättning filer om aktiviteten lyckas och en annan uppsättning filer om det misslyckas. Du kanske exempelvis vill överföra utförliga loggfiler när aktiviteten misslyckas och avslutas med en slutkod. På samma sätt kan du ladda upp filer för resultatet bara om aktiviteten lyckas, eftersom dessa filer kanske saknas eller är ofullständig om uppgiften misslyckas.

Kodexemplet ovan anger den **UploadCondition** egenskap **TaskCompletion**. Den här inställningen anger att filen ska överföras när aktiviteterna har slutförts, oavsett värdet för slutkoden.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Andra inställningar finns i den [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Disambiguate filer med samma namn

Aktiviteterna i ett jobb kan ge filer som har samma namn. Till exempel `stdout.txt` och `stderr.txt` skapas för varje aktivitet som körs i ett jobb. Eftersom varje aktivitet körs i sin egen kontexten kan de här filerna inte är i konflikt i nodens filsystemet. När du överför filer från flera aktiviteter till en delad behållare, måste du dock att förtydliga filer med samma namn.

Den [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) egenskap anger målblobben eller virtuell katalog för utdatafilerna. Du kan använda den **sökväg** egenskapen att namnge blob eller virtuell katalog så att utdatafilerna med samma namn är unikt namngivna i Azure Storage. Med hjälp av aktivitets-ID i sökvägen är ett bra sätt att se till att unika namn och enkelt identifiera filer.

Om den **FilePattern** är inställd på ett jokerteckenuttryck och sedan alla filer som matchar mönstret överförs till den virtuella katalogen som anges av den **sökväg** egenskapen. Exempel: om behållaren är `mycontainer`, uppgiften ID: T är `mytask`, och filmönstret är `..\std*.txt`, och sedan den absoluta URI: er till utdatafilerna i Azure Storage ska vara detsamma som:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Om den **FilePattern** är inställd på matcha ett enda filnamn, vilket innebär att den inte innehåller några jokertecken och sedan värdet för den **sökväg** egenskap anger fullständigt kvalificerade blobnamnet. Om du räknar namnkonflikter med en enda fil från flera aktiviteter, inkluderar du namnet på den virtuella katalogen som en del av namnet på filen för att undvika dessa filer. Till exempel den **sökväg** egenskapen att inkludera aktivitets-ID, avgränsningstecken (vanligtvis ett snedstreck) och namnet på filen:

`path: taskId + @"/output.txt"`

Absolut URI: er för utdata för en uppsättning uppgifter som ska vara detsamma som:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Mer information om virtuella kataloger i Azure Storage finns i [listar blobarna i en behållare](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostisera filen uppladdningsfel

Om inte går att överföra utdatafilerna till Azure Storage och sedan aktiviteten flyttas till den **slutförd** tillstånd och [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) egenskapen. Granska den **FailureInformation** egenskap för att undersöka vilka fel uppstod. Här är till exempel ett fel som uppstår på filuppladdning om det inte går att hitta behållaren:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

På varje filöverföringen skriver Batch två loggfiler till compute-nod `fileuploadout.txt` och `fileuploaderr.txt`. Du kan granska dessa loggfiler för mer information om ett specifikt fel. I fall där själva filuppladdningen aldrig gjordes, till exempel eftersom själva uppgiften gick inte att köra sedan finns loggfilerna inte.

## <a name="diagnose-file-upload-performance"></a>Diagnostisera prestandaproblem för uppladdning av filen

Den `fileuploadout.txt` filen loggar uppladdningsförloppet. Du kan kontrollera den här filen om du vill veta mer om hur lång tid tar din filöverföringar. Tänk på att det finns många bidragande faktorer att ladda upp prestanda, bland annat paketets storlek för noden, andra aktiviteter på noden vid tidpunkten för överföringen, om en målbehållare är i samma region som Batch-pool, hur många noder laddar upp till stora Ge kontot på samma gång och så vidare.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Använda API: et för Batch-tjänsten med Batch File Conventions-standard

När du bevara uppgiftens utdata med Batch-tjänst-API kan namnge din målbehållare och blobbar du själv vill. Du kan också välja att ge dem enligt den [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). File Conventions-standard anger namnen på Målbehållaren och blob i Azure Storage för en viss utdatafilen baserat på namnen på de jobb- och. Om du använder den File Conventions-standarden för namngivning av utdatafilerna så din utdatafilerna är tillgängliga för visning i den [Azure-portalen](https://portal.azure.com).

Om du utvecklar i C#, du kan använda de metoder som är inbyggda i den [Batch File Conventions-bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Det här biblioteket skapas korrekt namngivna behållare och blob-sökvägar. Du kan till exempel anropa API: et för att få rätt namn för den behållaren, baserat på jobbnamnet:

```csharp
string containerName = job.OutputStorageContainerName();
```

Du kan använda den [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metod för att returnera en URL för delad åtkomst (signatur) som används för att skriva till behållaren. Du kan sedan överföra den här SAS till den [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

Om du utvecklar i ett annat språk än C#, behöver du implementera File Conventions-standard själv.

## <a name="code-sample"></a>Kodexempel

Den [PersistOutputs] [ github_persistoutputs] exempelprojektet är en av de [Azure Batch-kodexempel] [ github_samples] på GitHub. Visual Studio-lösningen visar hur du använder Batch-klientbiblioteket för .NET för att spara uppgiftsutdata till beständig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2017**.
2. Lägg till ditt Batch- och Storage **kontoautentiseringsuppgifter** till **AccountSettings.settings** i Microsoft.Azure.Batch.Samples.Common-projektet.
3. **Skapa** (men inte kör) lösningen. Återställ NuGet-paket om du tillfrågas.
4. Använda Azure-portalen för att överföra en [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Inkludera den `PersistOutputsTask.exe` och dess beroende sammansättningar i ZIP-paketet, Ställ in program-ID till ”PersistOutputsTask” och paketversionen som programmet ska ”1.0”.
5. **Starta** (kör) den **PersistOutputs** projekt.
6. När du uppmanas att välja persistence-teknik för att använda för att köra exemplet, ange **2** att köra exemplet med API: et för Batch-tjänsten för att bevara uppgiftsutdata.
7. Om du vill köra exemplet igen, ange **3** att spara utdata med API: et för Batch-tjänsten och även för att namnge målsökväg för behållare och blobnamn enligt File Conventions-standarden.

## <a name="next-steps"></a>Nästa steg

- Mer information om bestående uppgiftens utdata med File Conventions-bibliotek för .NET finns i [bevara jobb- och data till Azure Storage med Batch File Conventions-bibliotek för .NET](batch-task-output-file-conventions.md).
- Information om andra metoder för att bevara utdata i Azure Batch finns i [Spara jobb- och utdata till Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
