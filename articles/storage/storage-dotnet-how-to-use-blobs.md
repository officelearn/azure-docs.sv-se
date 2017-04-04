---
title: "Komma igång med Azure Blob Storage (objektlagring) med hjälp av .NET | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 29d8693dd1d6d1ef26ccb21e3a5b29cf1adbfcc5
ms.lasthandoff: 03/28/2017

---
# <a name="get-started-with-azure-blob-storage-using-net"></a>Komma igång med Azure Blob Storage med hjälp av .NET

[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

### <a name="about-this-tutorial"></a>Om den här självstudiekursen
I den här kursen lär du dig hur du skriver .NET-kod för några vanliga scenarier med hjälp av Azure Blob Storage. I kursen beskrivs scenarier där du laddar upp, visar en lista över, laddar ned och tar bort blobbar.

**Krav:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Azure Configuration Manager för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Ett [Azure Storage-konto](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Fler exempel
Ytterligare exempel med Blob Storage finns i [Komma igång med Azure Blob Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Du kan ladda ned exempelprogrammet och köra det eller bläddra i koden på GitHub.

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv
Lägg till följande **using**-direktiv längst upp i filen `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Skapa klient för Blob-tjänst
Med **CloudBlobClient** kan du hämta behållare och blobbar som lagras i Blob Storage. Här är ett sätt att skapa tjänstklienten:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
Nu är det dags att skriva kod som läser data från och skriver data till Blob Storage.

## <a name="create-a-container"></a>Skapa en behållare
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Det här exemplet visas hur du skapar en behållare om den inte redan finns:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

Som standard är den nya behållaren privat, vilket innebär att du måste ange din lagringsåtkomstnyckel för att ladda ned blobbar från den här behållaren. Om du vill göra filerna i behållaren tillgängliga för alla kan du ange att behållaren ska vara offentlig med hjälp av följande kod:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

Alla på Internet kan se blobbar i en offentlig behållare. Du kan dock bara ändra eller ta bort dem om du har rätt kontoåtkomstnyckel eller signatur för delad åtkomst.

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Azure Blob Storage stöder blockblobbar och sidblobbar.  I de flesta fall är blockblob den rekommenderade typen.

Om du vill ladda upp en fil till en blockblobb hämtar du en referens för behållaren och använder den för att hämta en referens för blockblobben. När du har en blobbreferens kan du ladda upp en dataström till den genom att anropa metoden **UploadFromStream**. Den här åtgärden skapar blobben om den inte redan fanns, eller skriver över den om den finns.

Följande exempel visar hur du laddar upp en blobb till en behållare och förutsätter att behållaren redan hade skapats.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa blobbar i en behållare börjar du med att hämta en referens för behållaren. Sedan kan du använda behållarens **ListBlobs**-metod för att hämta blobbarna och/eller katalogerna i den. För att komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **IListBlobItem**-metod måste du skicka den till ett **CloudBlockBlob**-, **CloudPageBlob**- eller **CloudBlobDirectory**-objekt. Om typen är okänd kan du använda en typkontroll för att avgöra till vilket objekt den ska skickas. Följande kod visar hur du hämtar och returnerar URI:n för varje objekt i _photos_-behållaren:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Genom att ta med information om sökvägen i blobbnamnen kan du skapa en virtuell katalogstruktur som du kan ordna och bläddra i på samma sätt som ett traditionellt filsystem. Katalogstrukturen är bara virtuell – de enda tillgängliga resurserna i Blob Storage är behållare och blobbar. Klientbiblioteket tillhandahåller dock ett **CloudBlobDirectory**-objekt för att referera till en virtuell katalog och förenkla arbetet med blobbar som är ordnade på det här sättet.

Titta exempelvis på följande uppsättning blockblobbar i en behållare med namnet *photos*:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

När du anropar **ListBlobs** i *photos*-behållaren (som i föregående kodfragment) returneras en hierarkisk lista. Den innehåller både **CloudBlobDirectory**  och **CloudBlockBlob**objekt, som representerar kataloger respektive blobbar i behållaren. Resultatet ser ut så här:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Om du vill kan du ange parametern **UseFlatBlobListing** för **ListBlobs**-metoden till **True**. I detta fall returneras varje blobb i behållaren som ett **CloudBlockBlob**-objekt. Anropet till **ListBlobs** för att returnera en platt lista ser ut så här:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

och resultatet ser ut så här:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Ladda ned blobbar
Om du vill ladda ned blobbar börjar du med att hämta en blobbreferens och anropar sedan **DownloadToStream**-metoden. I följande exempel används metoden **DownloadToStream** för att överföra blobbinnehållet till ett dataströmsobjekt som du sedan kan spara till en lokal fil.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Du kan också använda metoden **DownloadToStream** för att ladda ned innehållet i en blobb som en textsträng.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Ta bort blobbar
Om du vill ta bort en blobb börjar du med att hämta en blobbreferens och anropar sedan metoden **Delete** för den.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Visa en lista över blobbar på sidor asynkront
Om du visar ett stort antal blobbar, eller om du vill styra hur många resultat som returneras i samma åtgärd, kan du visa blobbar på resultatsidor. Det här exemplet illustrerar hur du returnerar resultat på sidor asynkront, så att körningen inte blockeras när ett stort antal resultat väntar på att returneras.

I det här exemplet returneras en platt blobblista, men du kan också visa en hierarkiskt ordnad lista genom att ange _useFlatBlobListing_-parametern för **ListBlobsSegmentedAsync**-metoden till _false_.

Eftersom exempelmetoden anropar en asynkron metod måste den föregås av nyckelordet _async_ och returnera ett **Task**-objekt. Nyckelordet await som angetts för **ListBlobsSegmentedAsync**-metoden pausar körningen av exempelmetoden tills listuppgiften har slutförts.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>Skriva till en tilläggsblobb
En tilläggsblobb är optimerad för tilläggsåtgärder, t.ex loggning. Precis som en blockblobb består en tilläggsblobb av block, men när du lägger till ett nytt block till en tilläggsblobb läggs det alltid till sist i blobben. Du kan inte uppdatera eller ta bort ett befintligt block i en tilläggsblobb. En tilläggsblobbs block-ID:n exponeras inte som de gör för en blockblobb.

Blocken i en tilläggsblobb kan ha olika storlek, upp till högst 4 MB, och en tilläggsblobb kan innehålla högst 50 000 block. Den största storleken på en tilläggsblobb är alltså strax över 195 GB (4 MB × 50 000 block).

I exemplet nedan skapar vi en ny tilläggsblobb och lägger till vissa data i den för att simulera en enkel loggningsåtgärd.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

Mer information om skillnaderna mellan de tre typerna av blobbar finns i [Förstå blockblobbar, sidblobbar och tilläggsblobbar](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

## <a name="managing-security-for-blobs"></a>Hantera säkerheten för blobbar
Som standard skyddar Azure Storage dina data genom att begränsa åtkomsten till endast kontoägaren, som har tillgång till åtkomstnycklarna för kontot. När du behöver dela blobbdata i ditt lagringskonto är det viktigt att du gör det utan att äventyra åtkomstnycklarnas säkerhet. Du kan också kryptera blobbdata så att de är skyddade under kabelöverföringar och i Azure Storage.

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Kontrollera åtkomsten till blobbdata
Som standard är blobbdata i ett lagringskonto endast tillgängliga för lagringskontoägaren. För autentisering av förfrågningar mot Blob Storage krävs åtkomstnyckeln för kontot som standard. Dock kanske du vill göra vissa blobbdata tillgängliga för andra användare. Du kan välja mellan två alternativ:

* **Anonym åtkomst:** Du kan göra en behållare eller dess blobbar offentligt tillgängliga för anonym åtkomst. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](storage-manage-access-to-resources.md).
* **Signaturer för delad åtkomst:** Du kan ge klienterna en signatur för delad åtkomst (SAS), som ger delegerad åtkomst till en resurs i ditt lagringskonto, med behörigheter som du anger och under den period som du anger. Mer information finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="encrypting-blob-data"></a>Kryptera blobbdata
Azure Storage har stöd för kryptering av blobbdata både på klienten och på servern:

* **Kryptering på klientsidan:** Storage-klientbiblioteket för .NET har stöd för kryptering av data i klientprogram före överföringen till Azure Storage, och dekryptering av data under nedladdningen till klienten. Biblioteket stöder även integrering med Azure Key Vault för hantering av nycklar för lagringskonton. Mer information finns i [Kryptering på klientsidan med .NET för Microsoft Azure Storage](storage-client-side-encryption.md). Se även [Självstudier: Kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md).
* **Kryptering på serversidan**: Nu stöder Azure Storage kryptering på serversidan. Mer information finns i [Azure Storage Service-kryptering av vilande data (förhandsgranskning)](storage-service-encryption.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Blob Storage kan du följa dessa länkar om du vill veta mer.

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, Mac OS och Linux.

### <a name="blob-storage-samples"></a>Exempel på Blob Storage (förhandsversion)
* [Komma igång med Azure Blob Storage i .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referens för Blob Storage
* [Storage-klientbibliotek för .NET-referens](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [REST API-referens](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Begreppsorienterade guider
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Komma igång med File Storage för .NET](storage-dotnet-how-to-use-files.md)
* [Använda Azure Blob Storage med WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

