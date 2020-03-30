---
title: 'Snabbstart: Azure Blob storage client library for .NET Snabbstart: Azure Blob storage client library for .NET Snabbstart: Azure Blob storage client library for .NET Snabbstart'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage-klientbiblioteket för .NET för att skapa en behållare och en blob i Blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b243d05619642e1dd3ad8dfe2bbe1d0a9661b773
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75351304"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Snabbstart: Azure Blob storage client library v11 for .NET

Kom igång med Azure Blob Storage-klientbiblioteket v11 för .NET. Azure Blob Storage är Microsofts objektlagringslösning för molnet. Följ stegen för att installera paketet och prova exempelkod för grundläggande uppgifter. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data.

Använd Azure Blob Storage-klientbiblioteket för .NET för att:

* Skapa en container
* Ange behörigheter för en behållare
* Skapa en blob i Azure Storage
* Ladda ned blobben till din lokala dator
* Lista alla blobbar i en behållare
* Ta bort en container

[API-referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [Biblioteksnamnpaket](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [Exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuell [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Var noga med att få SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Blob Storage-klientbiblioteket för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa först ett .NET Core-program med namnet *blob-quickstart*.

1. I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du kommandot för att skapa en ny konsolapp med namnet *blob-quickstart*. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Växla till den nyskapade *blob-quickstart-mappen* och skapa appen för att kontrollera att allt är bra.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Den förväntade produktionen från bygga bör se ut ungefär så här:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Installera paketet

Installera azure Blob Storage-klientbiblioteket för .NET-paketet medan `dotnet add package` du fortfarande är i programkatalogen med kommandot.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna *Program.cs-filen* i redigeraren
2. Ta `Console.WriteLine` bort utdraget
3. Lägga `using` till direktiv
4. Skapa `ProcessAsync` en metod där huvudkoden för exemplet ska finnas
5. Asynkront `ProcessAsync` anropar metoden från`Main`

Här är koden:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static void Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            // Run the examples asynchronously, wait for the results before proceeding
            ProcessAsync().GetAwaiter().GetResult();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

När exempelprogrammet gör en begäran till Azure Storage måste det vara auktoriserat. Om du vill auktorisera en begäran lägger du till dina lagringskontouppgifter i programmet som en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot genom att följa dessa steg:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Här kan du visa åtkomstnycklarna för kontot och den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att välja **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

När du har kopierat anslutningssträngen ska du skriva den till en ny miljövariabel på den lokala dator där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `<yourconnectionstring>` med den faktiska anslutningssträngen.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

När du har lagt till miljövariabeln i Windows måste du starta en ny instans av kommandofönstret.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

När du har lagt till miljövariabeln startar du om alla program som körs som måste läsa miljövariabeln. Starta till exempel om utvecklingsmiljön eller redigeraren innan du fortsätter.

## <a name="object-model"></a>Objektmodell

Azure Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot.
* En container på lagringskontot
* En blobb i en container

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): `CloudStorageAccount` Klassen representerar ditt Azure-lagringskonto. Använd den här klassen om du vill auktorisera åtkomst till Blob-lagring med hjälp av dina kontoåtkomstnycklar.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): `CloudBlobClient` Klassen ger en åtkomstpunkt till Blob-tjänsten i din kod.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): `CloudBlobContainer` Klassen representerar en blob-behållare i din kod.
* [CloudBlockBlob:](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) `CloudBlockBlob` Objektet representerar en blockblob i din kod. Blockblobbar består av datablock som kan hanteras individuellt.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitten hur du utför följande med Azure Blob storage-klientbiblioteket för .NET:

   * [Autentisera klienten](#authenticate-the-client)
   * [Skapa en behållare](#create-a-container)
   * [Ange behörigheter för en behållare](#set-permissions-on-a-container)
   * [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
   * [Visa en lista över blobarna i en container](#list-the-blobs-in-a-container)
   * [Ladda ned blobbar](#download-blobs)
   * [Ta bort en container](#delete-a-container)

### <a name="authenticate-the-client"></a>Autentisera klienten

Koden nedan kontrollerar att miljövariabeln innehåller en anslutningssträng som kan tolkas för att skapa ett [CloudStorageAccount-objekt](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) som pekar på lagringskontot. Kontrollera att anslutningssträngen är giltig med hjälp av [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet)-metoden. Om `TryParse` lyckas initieras variabeln `storageAccount` och `true`returneras .

Lägg till den `ProcessAsync` här koden i metoden:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Om du vill utföra resten av `// ADD OTHER OPERATIONS HERE` åtgärderna i den här artikeln ersätter du kodavsnitten ovan med kodavsnitten i följande avsnitt.

### <a name="create-a-container"></a>Skapa en container

För att kunna skapa containern måste du först skapa en instans av objektet [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) som pekar på bloblagringen i lagringskontot. Skapa en instans av objektet [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) och skapa sedan behållaren.

I det här fallet anropar koden [metoden CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) för att skapa behållaren. Ett GUID-värde läggs till containerns namn så att det blir unikt. I en produktionsmiljö är det ofta att föredra att använda metoden [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) för att skapa en behållare endast om den inte redan finns.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Ange behörigheter för en behållare

Ange behörigheter för behållaren så att alla blobbar i behållaren är offentliga. Om en blob är offentlig kan den användas anonymt av alla klienter.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodavsnitt får en referens `CloudBlockBlob` till ett objekt genom att anropa metoden [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) på behållaren som skapats i föregående avsnitt. Den överför sedan den valda lokala filen till blobben genom att anropa metoden [UploadFromFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobbar i behållaren med metoden [ListBlobsSegmentedAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) I det här fallet har bara en blob lagts till i behållaren, så listningsåtgärden returnerar bara den bloben.

Om det finns för många blobbar för att returnera i ett anrop `ListBlobsSegmentedAsync` (som standard mer än 5000) returnerar metoden ett segment av den totala resultatuppsättningen och en fortsättningstoken. Om du vill hämta nästa segment blobar anger du det fortsättningstoken som returnerades av tidigare anrop, och så vidare tills fortsättningstokenet är null. Ett fortsättningstoken med nullvärde anger att alla blobar har hämtats. Koden visar hur du använder fortsättningstoken för bästa praxis.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta blobben som skapats tidigare i det lokala filsystemet med metoden [DownloadToFileAsync.](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) Exempelkoden lägger till ett suffix med "_DOWNLOADED" i blobnamnet så att du kan se båda filerna i det lokala filsystemet.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen har skapat genom att ta bort hela behållaren med [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Du kan även ta bort de lokala filerna om du vill.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i den lokala *mappen MyDocuments* och överför den till Blob-lagring. I exemplet visas sedan blobbar i behållaren och hämtar filen med ett nytt namn så att du kan jämföra gamla och nya filer.

Navigera till programkatalogen och skapa och kör programmet.

```console
dotnet build
```

```console
dotnet run
```

Utdata för appen liknar följande exempel:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

När du trycker på **Retur**-tangenten tas lagringscontainern och filerna bort. Innan du tar bort dem bör du kontrollera att de två filerna finns i mappen *Mina dokument*. Du kan öppna dem och se att de är identiska. Kopiera blobens URL från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i bloben.

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Om du vill veta hur du skapar en webbapp som överför en bild till Blob-lagring fortsätter du att:

> [!div class="nextstepaction"]
> [Ladda upp och bearbeta en avbildning](storage-upload-process-images.md)

* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
* Om du vill utforska ett exempelprogram som du kan distribuera från Visual Studio för Windows går du till [.NET Photo Gallery Web Application-exempel med Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
