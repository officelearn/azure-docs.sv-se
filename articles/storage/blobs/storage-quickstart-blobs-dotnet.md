---
title: Azure-snabbstart – Ladda upp, ladda ned och lista blobar i Azure Storage med .NET | Microsoft Docs
description: I den här snabbstarten skapar du ett lagringskonto och en behållare. Sedan använder du lagringsklientbiblioteket för .NET och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: a34a94a9421c65a2b1d4ce5c390732e0adbb69d6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med .NET

I den här snabbstarten får du lära dig att använda .NET-klientbiblioteket för Azure Storage för att ladda upp, ladda ned och lista blockblobar i en behållare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten måste du först skapa ett Azure-lagringskonto i [Azure-portalen](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Hjälp med att skapa kontot finns i [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md).

Härnäst laddar du ned och installerar .NET Core 2.0 för ditt operativsystem. Om du kör Windows kan du installera Visual Studio och använda .NET Framework om du så föredrar. Du kan också välja att installera en redigerare som ska användas med operativsystemet.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Installera [.NET Core för Windows](https://www.microsoft.com/net/download/windows) eller [.NET Framework](https://www.microsoft.com/net/download/windows) (ingår i Visual Studio för Windows)
- Installera [Visual Studio för Windows](https://www.visualstudio.com/). Om du använder .NET Core så är det valfritt för dig att installera Visual Studio.  

Information om hur du väljer mellan .NET Core och .NET Framework finns i [Välj mellan .NET Core och .NET Framework för serverappar](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Installera [.NET Core för Linux](https://www.microsoft.com/net/download/linux)
- Om du vill kan du installera [Visual Studio Code](https://www.visualstudio.com/) och [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Installera [.NET Core för macOS](https://www.microsoft.com/net/download/macos).
- Du kan också installera [Visual Studio för Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet som används i den här snabbstarten är ett grundläggande konsolprogram. Du kan utforska exempelprogrammet på [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösningen genom att leta reda på mappen *storage-blobs-dotnet-quickstart*, öppna den och dubbelklicka på *storage-blobs-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

För att kunna köra programmet måste du ange anslutningssträngen för ditt lagringskonto. Exempelprogrammet läser anslutningssträngen från en miljövariabel och använder den till att autentisera dina begäranden till Azure Storage.

När du har kopierat anslutningssträngen ska du skriva den till en ny miljövariabel på den lokala dator där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `<yourconnectionstring>` med den faktiska anslutningssträngen:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

När du har lagt till miljövariabeln kan du behöva starta om alla program som körs och som behöver läsa in miljövariabeln, däribland konsolfönstret. Om du t.ex. använder Visual Studio som redigeringsprogram, så starta om Visual Studio innan du kör exemplet. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Redigera din .bash_profile och lägg till miljövariabeln:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

---

## <a name="run-the-sample"></a>Kör exemplet

Det här exemplet skapar en testfil i din lokala **Mina dokument**-mapp och laddar upp den till BLOB-lagring. Exemplet listar sedan de blobar som finns i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Om du använder Visual Studio som redigeringsprogram kan du köra genom att trycka på **F5**. 

I annat fall går du till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Gå till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Gå till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

---

Exempelprogrammets utdata ser ut ungefär som i följande exempel:

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

När du trycker på **Retur**-tangenten tas lagringsbehållaren och filerna bort. Innan du tar bort dem bör du kontrollera att de två filerna finns i mappen **Mina dokument**. Du kan öppna dem och se att de är identiska. Kopiera blobens URL från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i bloben.

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen Program.cs och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst får du utforska exempelkoden så att du förstår hur den fungerar.

### <a name="try-parsing-the-connection-string"></a>Försök att parsa anslutningssträngen

Det första som exemplet gör är att kontrollera att miljövariabeln innehåller en anslutningssträng som kan parsas för att skapa ett [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount)-objekt som pekar på lagringskontot. Kontrollera att anslutningssträngen är giltig med hjälp av [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse)-metoden. Om **TryParse** lyckas, initieras variabeln *storageAccount* och **SANT** returneras.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Skapa behållaren och ange behörigheter

Sedan skapar exemplet en behållare och anger dess behörigheter så att alla blobar i behållaren är offentliga. Om en blob är offentlig kan den användas anonymt av alla klienter.

För att kunna skapa behållaren måste du först skapa en instans av objektet [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) som pekar på bloblagringen i lagringskontot. Skapa en instans av objektet [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) och skapa sedan behållaren. 

I det här fallet anropar exemplet metoden [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) för att skapa behållaren. Ett GUID-värde läggs till behållarens namn så att det blir unikt. I en produktionsmiljö är det ofta bäst att använda metoden [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) för att skapa en behållare endast om den inte redan finns, och undvika namnkonflikter.

> [!IMPORTANT]
> Behållarnamn måste använda gemener. Mer information om namngivning av behållare och blobar finns i [Namngivning och referens av behållare, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Exemplet laddar sedan upp en lokal fil till en blockblob. Kodexemplet hämtar en referens till ett **CloudBlockBlob**-objekt genom att anropa metoden [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) på den behållare som skapades i föregående avsnitt. Det laddar sedan upp den valda filen till bloben genom att anropa metoden [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Den här metoden skapar bloben om den inte redan finns, och skriver över den om den finns. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Exemplet listar blobarna i behållaren med metoden [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). I det här exemplet har endast en blob lagts till behållaren, så liståtgärden returnerar bara den bloben.

Om det finns för många blobar att returnera i ett anrop (som standard högst 5 000), returnerar metoden **ListBlobsSegmentedAsync** ett segment av den totala resultatuppsättningen och ett fortsättningstoken. Om du vill hämta nästa segment blobar anger du det fortsättningstoken som returnerades av tidigare anrop, och så vidare tills fortsättningstokenet är null. Ett fortsättningstoken med nullvärde anger att alla blobar har hämtats. Exempelkoden visar hur du använder fortsättningstoken för bästa praxis.

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
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Ladda ned blobbar

Därefter hämtar exemplet den blob som skapades tidigare till ditt lokala filsystem med metoden [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). Exempelkoden lägger till suffixet ”_DOWNLOADED” blobnamnet så att du kan se båda filerna i det lokala filsystemet.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Rensa resurser

Exemplet rensar upp de resurser som den som skapade genom att ta bort hela behållaren med hjälp av [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Du kan även ta bort de lokala filerna om du vill.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
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

## <a name="resources-for-developing-net-applications-with-blobs"></a>Resurser för utveckling av .NET-program med blobar

Se dessa ytterligare resurser för .NET-utveckling med Blob-lagring:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Ladda ned NuGet-paketet för den senaste versionen av [.NET-klientbiblioteket](https://www.nuget.org/packages/WindowsAzure.Storage/) för Azure Storage. 
- Visa [källkoden för .NET-klientbiblioteket](https://github.com/Azure/azure-storage-net) på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om klientbiblioteket för .NET finns i [API-referens för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Utforska [Blobblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) som skrivits med .NET-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av .NET. 

Om du vill lära dig att skapa en webbapp som laddar upp en bild till Blob-lagringen fortsätter du med [Ladda upp bilddata till molnet med Azure Storage](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-dotnet-how-to-use-blobs.md)

- Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
- Om du vill utforska ett exempelprogram som du kan distribuera från Visual Studio för Windows går du till [.NET Photo Gallery Web Application-exempel med Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 