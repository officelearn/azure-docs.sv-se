---
title: 'Snabbstart: Azure Blob storage-klientbiblioteket för .NET'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage-klientbiblioteket för .NET för att skapa en behållare och en blob i bloblagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: c5e9981c6854ff778775631f1d671189830e564b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435758"
---
# <a name="quickstart-azure-blob-storage-client-library-for-net"></a>Snabbstart: Azure Blob storage-klientbiblioteket för .NET

Kom igång med Azure Blob Storage-klientbiblioteket för .NET. Azure Blob Storage är Microsofts objekt för molnet. Följ stegen för att installera paketet och prova att använda kodexempel för grundläggande uppgifter. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data.

Använd Azure Blob Storage-klientbiblioteket för .NET för att:

* Skapa en container
* Ange behörigheter för en behållare
* Skapa en blob i Azure Storage
* Ladda ned bloben till din lokala dator
* Visa en lista över blobarna i en behållare
* Ta bort en container

[API-referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) | [bibliotekskällkod](https://github.com/Azure/azure-storage-net/tree/master/Blob) | [paketet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/) | [exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa ett kostnadsfritt](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core), eller senare för ditt operativsystem

## <a name="setting-up"></a>Ställa in

Det här avsnittet beskriver hur du förbereder ett projekt att arbeta med Azure Blob Storage-klientbiblioteket för .NET.

### <a name="create-the-project"></a>Skapa projektet

Börja med att skapa ett .NET Core-program med namnet **snabbstarten om blob**.

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash), använder den `dotnet new` kommando för att skapa en ny konsolapp med namnet **snabbstarten om blob**. Det här kommandot skapar en enkel ”Hello World” C# projekt med en enskild källfil: **Program.CS**.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Växla till den nyligen skapade **snabbstarten om blob** mappen och build app för att kontrollera att allt är korrekt.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

Förväntad utdata från bygget bör se ut ungefär så här:

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

När du fortfarande i programkatalogen, installerar du Azure Blob Storage-klientbiblioteket för .NET-paketet med hjälp av den `dotnet add package` kommando.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Skapa ett app-ramverk

Från projektkatalogen:

1. Öppna filen Program.cs i redigeringsprogrammet
2. Ta bort den **Console.WriteLine** instruktionen
3. Lägg till **med** direktiv
4. Skapa en **ProcessAsync** metod där huvudsakliga koden för det här exemplet ska placeras
5. Asynkront anropa den **ProcessAsync** metod från **Main**

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

Exempelprogrammet måste autentisera åtkomsten till lagringskontot. Lägg till autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot genom att följa dessa steg:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Här kan du visa åtkomstnycklarna för kontot och den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att välja **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

När du har kopierat anslutningssträngen ska du skriva den till en ny miljövariabel på den lokala dator där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `<yourconnectionstring>` med faktiska anslutningssträngen.

När du har lagt till miljövariabeln kan du behöva starta om alla program som behöver läsa in miljövariabeln körs. Till exempel om du använder Visual Studio som redigeringsprogram, starta om Visual Studio innan du kör exemplet.

#### <a name="windows"></a>Windows

```cmd
setx STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

#### <a name="linux"></a>Linux

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

## <a name="object-model"></a>Objektmodell

Azure Blob storage är optimerat för att lagra stora mängder Ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Storage-konto.
* En behållare i lagringskontot
* En blob i en behållare

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): Den **CloudStorageAccount** klassen som representerar ditt Azure storage-konto. Använd den här klassen för att bevilja åtkomst till Blob storage med åtkomstnycklarna för kontot.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): Den **CloudBlobClient** klassen innehåller en åtkomstpunkt till Blob service i din kod.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): Den **CloudBlobContainer** klassen representerar en blob-behållare i din kod.
* [CloudBlockBlob](//dotnet/api/microsoft.azure.storage.blob.cloudblockblob): Den **CloudBlockBlob** -objektet representerar en blockblob i din kod. Blockblobbar består av datablock som kan hanteras individuellt.

## <a name="code-examples"></a>Kodexempel

Dessa exempel kodfragment visar hur du utför följande med Azure Blob storage-klientbiblioteket för .NET:

   * [Autentisera klienten](#authenticate-the-client)
   * [Skapa en behållare](#create-a-container)
   * [Ange behörigheter för en behållare](#set-permissions-on-a-container)
   * [Ladda upp BLOB-objekt till en behållare](#upload-blobs-to-a-container)
   * [Lista blobarna i en behållare](#list-the-blobs-in-a-container)
   * [Ladda ned blobar](#download-blobs)
   * [Ta bort en container](#delete-a-container)

### <a name="authenticate-the-client"></a>Autentisera klienten

Koden nedan kontrollerar att miljövariabeln innehåller en anslutningssträng som kan parsas för att skapa en [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet) objekt som pekar på lagringskontot. Kontrollera att anslutningssträngen är giltig med hjälp av [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse?view=azure-dotnet)-metoden. Om **TryParse** lyckas, initieras variabeln *storageAccount* och **SANT** returneras.

Lägg till den här koden i den **ProcessAsync** metod:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("STORAGE_CONNECTION_STRING");

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
        "Add an environment variable named 'STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Om du vill utföra resten av åtgärderna i den här artikeln, Ersätt **/ / Lägg till andra åtgärder här** i koden ovan med kodfragment i följande avsnitt.

### <a name="create-a-container"></a>Skapa en container

För att kunna skapa containern måste du först skapa en instans av objektet [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient) som pekar på bloblagringen i lagringskontot. Skapa en instans av objektet [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) och skapa sedan behållaren.

I det här fallet koden anropar den [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) metod för att skapa behållaren. Ett GUID-värde läggs till containerns namn så att det blir unikt. I en produktionsmiljö är det ofta bäst att använda den [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) metod för att skapa en behållare endast om den inte redan finns.

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

Ange behörigheter för behållaren så att alla blobar i behållaren är offentliga. Om en blob är offentlig kan den användas anonymt av alla klienter.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp BLOB-objekt till en behållare

Följande kodavsnitt hämtar en referens till en **CloudBlockBlob** objekt genom att anropa den [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) metod för behållaren som skapades i föregående avsnitt. Det laddar sedan upp den valda lokala filen till bloben genom att anropa den [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) metod. Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns.

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

Listar blobarna i behållaren med hjälp av den [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync) metod. I det här fallet har endast en blob lagts till behållaren, så liståtgärden returnerar bara den bloben.

Om det finns för många blobar att returnera i ett anrop (som standard högst 5 000), returnerar metoden **ListBlobsSegmentedAsync** ett segment av den totala resultatuppsättningen och ett fortsättningstoken. Om du vill hämta nästa segment blobar anger du det fortsättningstoken som returnerades av tidigare anrop, och så vidare tills fortsättningstokenet är null. Ett fortsättningstoken med nullvärde anger att alla blobar har hämtats. Koden visar hur du använder fortsättningstoken för bästa praxis.

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

Ladda ned bloben som skapades tidigare till ditt lokala filsystem med hjälp av den [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metod. Exempelkoden lägger till suffixet ”_DOWNLOADED” blobnamnet så att du kan se båda filerna i lokala filsystemet.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod som rensar upp resurserna den app som skapats genom att ta bort hela behållaren med [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Du kan även ta bort de lokala filerna om du vill.

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

Den här appen skapar en testfil i din lokala **Mina dokument** mapp och överför dem till Blob storage. I exemplet sedan listar blobarna i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Om du använder Visual Studio som redigeringsprogram kan du köra genom att trycka på **F5**.

Annars går du till programkatalogen, och sedan skapa och kör programmet.

```console
dotnet build
```

```console
dotnet run
```

Utdata från exempelprogrammet som liknar följande exempel:

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

När du trycker på **Retur**-tangenten tas lagringscontainern och filerna bort. Innan du tar bort dem bör du kontrollera att de två filerna finns i mappen **Mina dokument**. Du kan öppna dem och se att de är identiska. Kopiera blobens URL från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i bloben.

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET.

Information om hur du skapar en webbapp som laddar upp en bild till Blob storage, fortsätter du till:

> [!div class="nextstepaction"]
> [Överföra och bearbeta en avbildning](storage-upload-process-images.md)

* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
* Om du vill utforska ett exempelprogram som du kan distribuera från Visual Studio för Windows går du till [.NET Photo Gallery Web Application-exempel med Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
