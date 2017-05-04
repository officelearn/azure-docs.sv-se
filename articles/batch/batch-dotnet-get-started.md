---
title: "Självstudiekurs – Använda Azure Batch-klientbiblioteket för .NET | Microsoft Docs"
description: "Lär dig de grundläggande principerna för Azure Batch och skapa en enkel lösning med .NET."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 91fab2cd7ad2babd567380308698f0608dda4cbf
ms.lasthandoff: 05/02/2017


---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Börja utveckla lösningar med Batch-klientbibliotek för .NET

> [!div class="op_single_selector"]
> * [NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Lär dig grunderna om [Azure Batch][azure_batch] och [Batch .NET][net_api]-biblioteket i den här artikeln där vi går igenom ett C#-exempelprogram steg för steg. Vi ska titta på hur exempelprogrammet utnyttjar Batch-tjänsten för att bearbeta en parallell arbetsbelastning i molnet och hur det interagerar med [Azure Storage](../storage/storage-introduction.md) för mellanlagring och hämtning av filer. Du får lära dig hur ett vanligt Batch-programarbetsflöde ser ut och får en grundläggande förståelse för de viktigaste komponenterna i Batch, t.ex. jobb, aktiviteter, pooler och beräkningsnoder.

![Arbetsflöde för Batch-lösning (grundläggande)][11]<br/>

## <a name="prerequisites"></a>Krav
I den här artikeln förutsätter vi att du har erfarenhet av att arbeta med C# och Visual Studio. Vi förutsätter också att du uppfyller kontokraven som anges nedan för Azure och Batch- och Storage-tjänsterna.

### <a name="accounts"></a>Konton
* **Azure-konto**: Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt Azure-konto][azure_free_account].
* **Batch-konto**: När du har skaffat en Azure-prenumeration [skapar du ett Azure Batch-konto](batch-account-create-portal.md).
* **Lagringskonto**: Se [skapar ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account) i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md).

> [!IMPORTANT]
> Batch stöder för närvarande *endast* den **allmänna** lagringskontotypen, vilket förklaras i steg 5, [skapar ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account), i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Du måste ha **Visual Studio 2015** eller senare för att kunna skapa exempelprojektet. Du hittar kostnadsfria versioner och utvärderingsversioner av Visual Studio i [Översikt över Visual Studio-produkter][visual_studio].

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial*kodexempel
[DotNetTutorial][github_dotnettutorial]-exemplet är ett av många Batch-kodexempel i [azure-batch-samples][github_samples]-databasen på GitHub. Du kan ladda ned alla exempel genom att klicka på **Klona eller ladda ned > Ladda ned ZIP** på startsidan för databasen eller genom att klicka på direktnedladdningslänken för [azure-batch-samples-master.zip][github_samples_zip]. När du har extraherat innehållet i ZIP-filen hittar du lösningen i följande mapp:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure Batch Explorer (valfritt)
[Azure Batch Explorer][github_batchexplorer] är ett kostnadsfritt verktyg som ingår i [azure-batch-samples][github_samples]-databasen på GitHub. Det krävs inte för att slutföra den här kursen men kan vara praktiskt när du skapar och felsöker Batch-lösningar.

## <a name="dotnettutorial-sample-project-overview"></a>Översikt över DotNetTutorial-exempelprojekt
*DotNetTutorial*-kodexemplet är en Visual Studio-lösning som består av två projekt: **DotNetTutorial** och **TaskApplication**.

* **DotNetTutorial** är klientprogrammet som samverkar med Batch- och Storage-tjänsterna för att köra en parallell arbetsbelastning på beräkningsnoder (virtuella datorer). DotNetTutorial körs på den lokala arbetsstationen.
* **TaskApplication** är programmet som körs på beräkningsnoder i Azure för att utföra det faktiska arbetet. I exemplet parsar `TaskApplication.exe` texten i en fil som hämtats från Azure Storage (indatafilen). Därefter genererar den en textfil (utdatafilen) som innehåller en lista med de tre översta orden i indatafilen. När utdatafilen har skapats laddar TaskApplication upp filen till Azure Storage. På så sätt blir den tillgänglig för klientprogrammet för nedladdning. TaskApplication körs parallellt på flera beräkningsnoder i Batch-tjänsten.

Följande diagram illustrerar de viktigaste åtgärderna som utförs av klientprogrammet, *DotNetTutorial* och programmet som körs av aktiviteterna, *TaskApplication*. Detta grundläggande arbetsflöde är typiskt för många beräkningslösningar som skapas med Batch. Det illustrerar inte alla funktioner som är tillgängliga i Batch-tjänsten, men delar av det här arbetsflödet ingår i nästan alla Batch-scenarier.

![Batch-exempelarbetsflöde][8]<br/>

[**Steg 1.**](#step-1-create-storage-containers) Skapa **behållare** i Azure Blob Storage.<br/>
[**Steg 2.**](#step-2-upload-task-application-and-data-files) Ladda upp aktivitetsprogramfiler och indatafiler till behållare.<br/>
[**Steg 3.**](#step-3-create-batch-pool) Skapa en Batch-**pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** Poolens **StartTask** hämtar binärfilerna för aktiviteter (TaskApplication) till noder när de ansluter till poolen.<br/>
[**Steg 4.**](#step-4-create-batch-job) Skapa ett Batch-**jobb**.<br/>
[**Steg 5.**](#step-5-add-tasks-to-job) Lägg till **aktiviteter** till jobbet.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Aktiviteterna schemaläggs att köras på noder.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Varje aktivitet hämtar sina indata från Azure Storage och börjar sedan köra.<br/>
[**Steg 6.**](#step-6-monitor-tasks) Övervaka aktiviteter.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Allt eftersom aktiviteterna slutförs överför de sina utdata till Azure Storage.<br/>
[**Step 7.**](#step-7-download-task-output) Hämta aktiviteternas utdata från Storage.

Som vi redan nämnt utförs inte exakt dessa steg i alla Batch-lösningar, och många lösningar omfattar många fler steg, men *DotNetTutorial*-exempelprogrammet demonstrerar vanliga processer som återfinns i många Batch-lösningar.

## <a name="build-the-dotnettutorial-sample-project"></a>Skapa *DotNetTutorial*-exempelprojektet
Innan du kan köra exemplet måste du ange autentiseringsuppgifter både för Batch och Storage i *DotNetTutorial*-projektets `Program.cs`-fil. Om du inte redan har gjort det öppnar du lösningen i Visual Studio genom att dubbelklicka på `DotNetTutorial.sln`-lösningsfilen. Du kan också öppna den från Visual Studio från menyn **Arkiv > Öppna > Projekt/lösning**.

Öppna `Program.cs` i *DotNetTutorial*-projektet. Följ uppmaningen längst upp i filen och lägg till dina autentiseringsuppgifter:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Som vi redan nämnt måste du för närvarande ange autentiseringsuppgifterna för ett **allmänt** lagringskontot i Azure Storage. Dina Batch-program använder Blob Storage i det **allmänna** lagringskontot. Ange inte autentiseringsuppgifterna för ett Storage-konto som skapats med kontotypen *Blob Storage*.
>
>

Du hittar dina autentiseringsuppgifter för Batch och Storage på kontobladet för respektive tjänst på [Azure Portal][azure_portal]:

![Batch-autentiseringsuppgifter på portalen][9]
![Storage-autentiseringsuppgifter på portalen][10]<br/>

Nu när du har uppdaterat projektet med dina autentiseringsuppgifter högerklickar du på lösningen i Solution Explorer och klickar på **Skapa lösning**. Bekräfta återställningen av NuGet-paket om du uppmanas att göra det.

> [!TIP]
> Om NuGet-paketen inte återställs automatiskt, eller om du får fel som meddelar att det inte går att återställa paketen, kontrollerar du att [NuGet Package Manager][nuget_packagemgr] är installerat. Därefter aktiverar du hämtningen av paketen som saknas. Mer information om hur du aktiverar pakethämtning finns i [Enabling Package Restore During Build][nuget_restore] (Aktivera paketåterställning under utvecklingen).
>
>

I följande avsnitt bryter vi ned exempelprogrammet i de steg som det utför för att bearbeta en arbetsbelastning i Batch-tjänsten. Stegen beskrivs i detalj. Vi rekommenderar att du följer med i den öppna lösningen i Visual Studio medan du gå igenom resten av den här artikeln eftersom inte alla kodrader i exemplet beskrivs.

Gå till början av `MainAsync`-metoden i *DotNetTutorial*-projektets `Program.cs`-fil för att börja med steg 1. Varje steg nedan följer sedan i stora drag processen för metodanrop i `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Steg 1: Skapa Storage-behållare
![Skapa behållare i Azure Storage][1]
<br/>

Batch har inbyggt stöd för integrering med Azure Storage. Behållare i ditt Storage-konto tillhandahåller de filer som behövs av aktiviteterna som körs i ditt Batch-konto. Behållarna tillhandahåller även en plats för att lagra utdata som genereras av aktiviteterna. Det första som *DotNetTutorial*-klientprogrammet gör är att skapa tre behållare i [Azure Blob Storage](../storage/storage-introduction.md):

* **application**: Den här behållaren lagrar programmet som körs av aktiviteterna, samt eventuella beroenden, till exempel DLL-filer.
* **input**: Aktiviteterna hämtar datafilerna som ska bearbetas från *input*-behållaren.
* **output**: När aktiviteterna har bearbetat indatafilen laddas resultatet upp till *output*-behållaren.

För att interagera med ett Storage-konto och skapa behållare använder vi [Azure Storage-klientbiblioteket för .NET][net_api_storage]. Vi skapar en referens till kontot med [CloudStorageAccount][net_cloudstorageaccount], och därifrån skapar vi en [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Vi använder `blobClient`-referensen i hela programmet och skickar den som en parameter till flera metoder. Ett exempel på detta finns i kodblocket som kommer direkt efter koden ovan, där vi anropar `CreateContainerIfNotExistAsync` för att skapa behållarna.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

När behållarna har skapats kan programmet ladda upp filerna som ska användas av aktiviteterna.

> [!TIP]
> [Använda Blob Storage från .NET](../storage/storage-dotnet-how-to-use-blobs.md) innehåller en bra översikt över hur du arbetar med behållare och blobbar i Azure. Du hittar avsnittet nästan längst upp i läslistan när du börjar arbeta med Batch.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Steg 2: Ladda upp filer för aktivitetsprogram och indata
![Ladda upp filer för aktivitetsprogram och indata till behållare][2]
<br/>

I filuppladdningsåtgärden definierar *DotNetTutorial* först samlingar med sökvägar till **program-** och **indatafiler** på den lokala datorn. Därefter laddas dessa filer upp till behållarna som du skapade i föregående steg.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Två metoder i `Program.cs` används vid uppladdningen:

* `UploadFilesToContainerAsync`: Den här metoden returnerar en samling [ResourceFile][net_resourcefile]-objekt (se nedan) och anropar `UploadFileToContainerAsync` internt för att ladda upp varje fil som anges i parametern *filePaths*.
* `UploadFileToContainerAsync`: Det här är den metod som utför själva filuppladdningen och skapar [ResourceFile][net_resourcefile]-objekten. När filen har laddats upp hämtar den en signatur för delad åtkomst (SAS) för filen och returnerar ett ResourceFile-objekt som representerar den. Signaturer för delad åtkomst beskrivs nedan.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
En [ResourceFile][net_resourcefile] definierar aktiviteter i Batch med URL-adressen till en fil i Azure Storage som laddas ned till en beräkningsnod innan aktiviteten körs. Egenskapen [ResourceFile.BlobSource][net_resourcefile_blobsource] anger den fullständiga URL:en för filen i Azure Storage. URL-adressen kan även innehålla en signatur för delad åtkomst (SAS) som ger säker åtkomst till filen. De flesta typer av aktiviteter i Batch .NET innehåller en *ResourceFiles*-egenskap, inklusive:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

I DotNetTutorial-exempelprogrammet används inte aktivitetstyperna JobPreparationTask och JobReleaseTask, men du kan läsa mer om dem i [Köra jobbförberedelse- och jobbpubliceringsaktiviteter på Azure Batch-beräkningsnoder](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Signatur för delad åtkomst (SAS)
Signaturer för delad åtkomst är strängar som, när de är en del av en URL, ger säker åtkomst till behållare och blobbar i Azure Storage. DotNetTutorial-programmet använder URL:er med signaturer för delad åtkomst både för blobbar och behållare och visar hur du skaffar signatursträngarna för delad åtkomst från Storage-tjänsten.

* **Signaturer för delad åtkomst för blobbar**: Poolens StartTask i DotNetTutorial använder signaturer för delad åtkomst för blobbar när den laddar ned programmets binärfiler och indatafilerna från Storage (se steg 3 nedan). `UploadFileToContainerAsync`-metoden i DotNetTutorials `Program.cs` innehåller koden som hämtar varje blobbs signatur för delad åtkomst. Detta sker genom anrop till [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Signaturer för delad åtkomst för behållare**: När en aktivitet har slutfört arbetet på en beräkningsnod laddar den upp sina utdatafiler till *utdatabehållaren* i Azure Storage. För att göra det använder TaskApplication en signatur för delad åtkomst som ger skrivåtkomst till behållaren som en del av sökvägen när filen laddas upp. Signaturer för delad åtkomst för behållare hämtas i princip på samma sätt som signaturer för delad åtkomst för blobbar. I DotNetTutorial anropar `GetContainerSasUrl`-hjälpmetoden [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] för att göra det. Du kan läsa mer om hur TaskApplication använder signaturen för delad åtkomst för behållare i ”Steg 6: Övervaka aktiviteter”.

> [!TIP]
> Läs serien i två delar om signaturer för delad åtkomst, [Del 1: Förstå modellen för signaturer för delad åtkomst (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) och [Del 2: Skapa och använda en signatur för delad åtkomst (SAS) med Blob Storage](../storage/storage-dotnet-shared-access-signature-part-2.md) om du vill veta mer om hur du kan konfigurera säker åtkomst till data i ditt Storage-konto.
>
>

## <a name="step-3-create-batch-pool"></a>Steg 3: Skapa en Batch-pool
![Skapa en Batch-pool][3]
<br/>

En Batch-**pool** är en samling beräkningsnoder (virtuella datorer) där Batch utför aktiviteterna i ett jobb.

När *DotNetTutorial* har laddat upp program- och datafilerna till Storage-kontot börjar programmet interagera med Batch-tjänsten med hjälp av Batch-biblioteket för .NET. För att göra detta skapas först en [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Därefter skapas en pool med beräkningsnoder i Batch-kontot med ett anrop till `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` använder metoden [BatchClient.PoolOperations.CreatePool][net_pool_create] för att skapa en pool i Batch-tjänsten.

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,                                                         // 3 compute nodes
            virtualMachineSize: "small",                                                // single-core, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

När du skapar en pool med [CreatePool][net_pool_create] anger du flera parametrar, t.ex. antalet beräkningsnoder, [storleken på noderna](../cloud-services/cloud-services-sizes-specs.md) och nodernas operativsystem. I *DotNetTutorial* använder vi [CloudServiceConfiguration][net_cloudserviceconfiguration] för att ange Windows Server 2012 R2 från [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). Men om du i stället anger en [VirtualMachineConfiguration][net_virtualmachineconfiguration] kan du skapa pooler med noder som skapats från Marketplace-avbildningar, som innehåller både Windows- och Linux-avbildningar. Mer information finns i [Etablera Linux-beräkningsnoder i Azure Batch-pooler](batch-linux-nodes.md).

> [!IMPORTANT]
> Du debiteras för beräkningsresurser i Batch. Du kan minimera kostnaderna genom att sänka `targetDedicated` till 1 innan du kör exemplet.
>
>

Tillsammans med dessa fysiska nodegenskaper kan du även ange en [StartTask][net_pool_starttask] för poolen. StartTask körs på varje nod när noden ansluter till poolen och varje gång en nod startas om. StartTask är särskilt användbar för att installera program på beräkningsnoder innan aktiviteterna körs. Om aktiviteterna exempelvis bearbetar data med hjälp av Python-skript kan du använda en StartTask för att installera Python på beräkningsnoderna.

I det här exempelprogrammet kopierar StartTask filerna som hämtats från Storage (som anges med egenskapen [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) från StartTask-arbetskatalogen till den delade katalogen som *alla* aktiviteter som körs på noden kan komma åt. Vad som sker är att `TaskApplication.exe` och dess beroenden kopieras till den delade katalogen på varje nod när noden läggs till i poolen, så att alla aktiviteter som körs på noden kan komma åt den.

> [!TIP]
> Ett annat sätt att få programmet till beräkningsnoderna i en pool är att använda funktionen för **programpaket** i Azure Batch. Mer information finns i [Programdistribution med Azure Batch-programpaket](batch-application-packages.md).
>
>

Något annat att notera i kodfragmentet ovan är användningen av två miljövariabler i egenskapen *CommandLine* för StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` och `%AZ_BATCH_NODE_SHARED_DIR%`. Varje beräkningsnod i en Batch-pool konfigureras automatiskt med flera miljövariabler som är specifika för Batch. En process som körs av en aktivitet har åtkomst till dessa miljövariabler.

> [!TIP]
> Mer information om miljövariablerna som är tillgängliga på beräkningsnoder i en Batch-pool, samt information om arbetskataloger för aktiviteter, finns i avsnitten [Miljöinställningar för aktiviteter](batch-api-basics.md#environment-settings-for-tasks) och [Filer och kataloger](batch-api-basics.md#files-and-directories) i [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Steg 4: Skapa ett Batch-jobb
![Skapa ett Batch-jobb][4]<br/>

Ett Batch-**jobb** är en samling aktiviteter och associeras med en pool av beräkningsnoder. Aktiviteterna i ett jobb körs på den associerade poolens beräkningsnoder.

Förutom att använda ett jobb för att ordna och spåra uppgifter i relaterade arbetsbelastningar kan du använda det för att tillämpa vissa begränsningar, t.ex. jobbets (och därmed även aktiviteternas) maximala körtid, samt jobbprioritet i förhållande till andra jobb i Batch-kontot. I det här exemplet är jobbet dock associerat endast med poolen som skapades i steg 3. Inga ytterligare egenskaper har konfigurerats.

Alla Batch-jobb är associerade med en specifik pool. Den här associationen anger vilka noder som jobbets aktiviteter ska köras på. Du anger detta genom att använda egenskapen [CloudJob.PoolInformation][net_job_poolinfo], som du ser i kodfragmentet nedan.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Nu när jobbet har skapats är det dags att lägga till aktiviteterna som ska utföra arbetet.

## <a name="step-5-add-tasks-to-job"></a>Steg 5: Lägga till aktiviteter till ett jobb
![Lägga till aktiviteter till ett jobb][5]<br/>
*(1) Aktiviteterna läggs till i jobbet, (2) aktiviteterna schemaläggs för att köras på noder och (3) aktiviteterna hämtar datafilerna som ska bearbetas*

Batch-**aktiviteter** är enskilda arbetsenheter som körs på beräkningsnoderna. En aktivitet har en kommandorad och kör skripten eller de körbara filer som du anger på kommandoraden.

För att arbetet ska utföras måste aktiviteter läggas till i ett jobb. Varje [CloudTask][net_task] konfigureras med hjälp av en kommandoradsegenskap och [ResourceFiles][net_task_resourcefiles] (som med poolens StartTask) som aktiviteten hämtar till noden innan dess kommandorad körs automatiskt. I *DotNetTutorial*-exempelprojektet bearbetar varje aktivitet endast en fil. Det betyder att dess ResourceFiles-samling kan innehålla ett enda element.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> När kommandorader för aktiviteter ska komma åt miljövariabler som `%AZ_BATCH_NODE_SHARED_DIR%` eller köra ett program som inte finns i nodens `PATH` måste de föregås av prefixet `cmd /c`. Detta utlöser kommandotolken och instruerar den att avsluta när kommandot har körts. Du kan bortse från det här kravet om aktiviteterna kör ett program i nodens `PATH` (exempelvis *robocopy.exe* eller *powershell.exe*) och inga miljövariabler används.
>
>

I `foreach`-loopen i kodfragmentet ovan kan du se att kommandoraden för aktiviteten är konstruerad så att tre kommandoradsargument skickas till *TaskApplication.exe*:

1. Det **första argumentet** är sökvägen till filen som ska bearbetas. Det här är den lokala sökvägen till filen på noden. När ResourceFile-objektet i `UploadFileToContainerAsync` skapades ovan användes filnamnet för den här egenskapen (som en parameter till ResourceFile-konstruktorn). Detta anger att filen finns i samma katalog som *TaskApplication.exe*.
2. Det **andra argumentet** anger att de översta *N* orden ska skrivas till utdatafilen. I exemplet är detta hårdkodat så att de tre översta orden skrivs till utdatafilen.
3. Det **tredje argumentet** är signaturen för delad åtkomst (SAS) som ger skrivåtkomst till **utdatabehållaren** i Azure Storage. *TaskApplication.exe* använder den här URL:en med en signatur för delad åtkomst när utdatafilen laddas upp till Azure Storage. Du hittar koden för detta i `UploadFileToContainer`-metoden i TaskApplication-projektets `Program.cs`-fil:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Steg 6: Övervaka aktiviteter
![Övervaka aktiviteter][6]<br/>
*Klientprogrammet (1) övervakar aktiviteterna och ser till att de slutförs korrekt och (2) aktiviteterna laddar upp resultatdata till Azure Storage*

När aktiviteter läggs till i ett jobb placeras de automatiskt i kö och schemaläggs för körning på beräkningsnoder i poolen som är associerad med jobbet. Baserat på de inställningar du anger sköter Batch all köhantering, all schemaläggning, alla omförsök och all annan aktivitetsadministration åt dig.

Du kan övervaka aktivitetskörningen på många sätt. DotNetTutorial visar ett enkelt exempel som endast rapporterar om aktiviteterna har slutförts eller misslyckats, samt aktiviteternas status. `MonitorTasks`-metoden i DotNetTutorials `Program.cs` innehåller tre viktiga Batch-relaterade .NET-begrepp som beskrivs nedan. De förklaras nedan i den ordning som de förekommer:

1. **ODATADetailLevel**: Det är viktigt att du anger [ODATADetailLevel][net_odatadetaillevel] i liståtgärder (till exempel när du hämtar en lista över aktiviteterna i ett jobb) för att upprätthålla Batch-programmets prestanda. Lägg till [Visa information om Azure Batch-tjänstens effektivitet](batch-efficient-list-queries.md) i din läslista om du vill övervaka statusen i dina Batch-program.
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] tillhandahåller .NET-program för Batch med verktyg som hjälper dig att övervaka aktiviteternas status. I `MonitorTasks` väntar *DotNetTutorial* tills alla aktiviteter fått statusen [TaskState.Completed][net_taskstate] inom en viss tidsgräns. Därefter avslutas jobbet.
3. **TerminateJobAsync**: När ett jobb avslutas med [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (eller blockeras med JobOperations.TerminateJob) markeras jobbet som slutfört. Det är viktigt att du gör det om Batch-lösningen använder en [JobReleaseTask][net_jobreltask]. Det här är en särskild typ av aktivitet, som beskrivs i [Aktiviteter för att förbereda och slutföra jobb](batch-job-prep-release.md).

`MonitorTasks`-metoden från *DotNetTutorial*s `Program.cs` visas nedan:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Steg 7: Hämta aktivitetsutdata
![Hämta aktivitetsutdata från Storage][7]<br/>

Nu när jobbet har slutförts kan du hämta aktivietsutdata från Azure Storage. Detta görs med ett anrop till `DownloadBlobsFromContainerAsync` i *DotNetTutorial*s `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> Anropet till `DownloadBlobsFromContainerAsync` i *DotNetTutorial*-programmet anger att filerna ska hämtas till din `%TEMP%`-mapp. Ändra platsen för utdata om du vill.
>
>

## <a name="step-8-delete-containers"></a>Steg 8: Ta bort behållare
Eftersom du debiteras för data som finns i Azure Storage är det alltid en bra idé att ta bort alla blobar som inte längre behövs för dina Batch-jobb. I DotNetTutorials `Program.cs` görs detta med tre anrop till hjälpmetoden `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Själva metoden hämtar bara en referens till behållaren och anropar sedan [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Steg 9: Ta bort jobbet och poolen
I det sista steget uppmanas du att ta bort jobbet och poolen som skapats av DotNetTutorial-programmet. Även om du inte debiteras för själva jobben och aktiviteterna *debiteras* du för beräkningsnoder. Vi rekommenderar därför att du endast allokerar noder efter behov. Borttagning av oanvända pooler kan ingå i din underhållsrutin.

BatchClients [JobOperations][net_joboperations] och [PoolOperations][net_pooloperations] har båda motsvarande borttagningsmetoder, som anropas om användaren bekräftar borttagningen:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Tänk på att du debiteras för beräkningsresurser – du kan minimera kostnaden genom att ta bort oanvända pooler. Tänk på att om du tar bort en pool så tas alla beräkningsnoder i poolen bort. Tänk också på att data på noderna inte kan återställas när poolen har tagits bort.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Kör *DotNetTutorial*-exemplet
När du kör exempelprogrammet ser konsolens utdata ut ungefär så här: Under körningen uppstår det en paus vid `Awaiting task completion, timeout in 00:30:00...` medan poolens beräkningsnoder startas. Använd [Azure Portal][azure_portal] när du vill övervaka poolen, beräkningsnoderna, jobbet och aktiviteterna under och efter körningen. Använd [Azure Portal][azure_portal] eller [Azure Storage Explorer][storage_explorers] för att visa de lagringsresurser (behållare och blobar) som skapas av programmet.

Körningen tar normalt **runt fem minuter** om du kör programmet med standardkonfigurationen.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Nästa steg
Gör gärna ändringar i *DotNetTutorial* och *TaskApplication* för att experimentera med olika beräkningsscenarier. Prova till exempel att lägga till en körningsfördröjning till *TaskApplication*, till exempel med [Thread.Sleep][net_thread_sleep], om du vill simulera långvariga aktiviteter och övervaka dem på portalen. Prova att lägga till fler aktiviteter eller att justera antalet beräkningsnoder. Lägg till logik om du vill söka efter och tillåta användningen av en befintlig pool för att förkorta körningstiden (*Tips!* Se `ArticleHelpers.cs` i [Microsoft.Azure.Batch.Samples.Common][github_samples_common]-projektet i [azure-batch-samples][github_samples]).

Nu när du har bekantat dig med det grundläggande arbetsflödet i en Batch-lösning är det dags att titta närmare på de andra funktionerna i Batch-tjänsten.

* Läs artikeln [Översikt över Azure Batch-funktioner](batch-api-basics.md), som vi  rekommenderar om du inte har använt tjänsten.
* Sätt igång med de andra Batch-utvecklingsartiklarna under **Utveckling på djupet** i [utbildningsvägen för Batch][batch_learning_path].
* Ta en titt på en annan implementering av arbetsbelastningen ”översta N orden” med hjälp av Batch i [TopNWords][github_topnwords]-exemplet.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Skapa behållare i Azure Storage"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Ladda upp filer för aktivitetsprogram och indata till behållare"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Skapa en Batch-pool"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Skapa ett Batch-jobb"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Lägga till aktiviteter till ett jobb"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Övervaka aktiviteter"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Hämta aktivitetsutdata från Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Arbetsflödet i en Batch-lösning (fullständigt diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Batch-autentiseringsuppgifter på portalen"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Storage-autentiseringsuppgifter på portalen"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Arbetsflödet i en Batch-lösning (minimalt diagram)"

