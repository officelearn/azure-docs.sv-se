---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av .NET | Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av .NET"
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: ca4cb2dea9cdd2e46c3aef042e525acdfc09de8e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Överför objekt till/från Azure Blob storage med hjälp av .NET

Lär dig hur du använder .NET-klientbiblioteket för Azure Storage att överföra, hämta och visa en lista med blockblobbar i en behållare i den här snabbstarten.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten, installera [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med följande arbetsbelastning:

- **Azure Development**

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) att hämta en kopia av programmet till din utvecklingsmiljö. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Det här kommandot klonar databasen till din lokala git-mapp. Öppna Visual Studio-lösning, letar du upp mappen storage-BLOB-dotnet-Snabbstart, öppna det och dubbelklicka på storage-BLOB-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna den `app.config` filen i Solutions Explorer i Visual Studio. Hitta den `StorageConnectionString` posten. För **värdet**, Ersätt hela värdet för anslutningssträngen med den du sparade från Azure-portalen. Din `storageConnectionString` bör likna följande:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=<NameHere>;
    AccountKey=<KeyHere>" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Köra exemplet

Det här exemplet skapar en testfil i Mina dokument, överför det till Blob storage, visar en lista över blobbar i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

Köra exemplet genom att trycka på F5. Den visar resultatet i ett konsolfönster som liknar följande: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

När du trycker på valfri tangent för att fortsätta tas bort lagringsbehållare och filer. Kontrollera Mina dokument för de två filerna innan du fortsätter. Du kan öppna dem och de är identiska. Kopiera URL-Adressen för blob utanför konsolfönstret och klistra in den i en webbläsare för att visa innehållet i filen i Blob storage.

Du kan också använda ett verktyg som den [Azure Lagringsutforskaren](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) att visa filer i Blob storage. Azure Lagringsutforskaren är ett kostnadsfritt plattformsoberoende verktyg som hjälper dig att komma åt kontoinformationen lagring. 

När du har kontrollerat filerna, trycka på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör du öppna filen Program.cs titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Därefter går vi igenom exempelkoden så att du förstår hur det fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekt

Det första du gör är att skapa referenser till objekt som används för att komma åt och hantera Blob storage. De här objekten som bygger på varandra – varje används av den nästa i listan.

* Skapa en instans av den **CloudStorageAccount** objekt som pekar på lagringskontot. 

* Skapa en instans av den **CloudBlobClient** -objektet, vilket leder till Blob-tjänsten på ditt lagringskonto. 

* Skapa en instans av den **CloudBlobContainer** objekt som representerar den behållare som du ansluter till. Behållare för att organisera dina blobbar som du använder mappar på din dator för att ordna dina filer.

När du har den **CloudBlobContainer**, du kan skapa en instans av den **CloudBlockBlob** objekt som hänvisar till specifika blob som du är intresserad av och utför en överföringen, hämta, kopiera och så vidare. åtgärden.

> [!IMPORTANT]
> Behållarnamn måste vara gemener. Se [namnge och referera till behållare, Blobbar och Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) för mer information om behållaren och blob-namn.

I det här avsnittet, skapa en instans av objekten, skapa en ny behållare och ange behörigheter för behållaren så att blobbarna är offentliga och kan nås med bara en URL. Behållaren kallas **quickstartblobs**. 

Det här exemplet används **CreateIfNotExists** eftersom vi vill skapa en ny behållare varje gång exemplet körs. I en produktionsmiljö där du använder samma behållare i ett program, det är bättre att bara anropa **CreateIfNotExists** när. Du kan också behöver för att skapa behållaren i förväg så att du inte skapa i koden.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobbar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobbar är de vanligaste och som används i denna Snabbstart. 

Hämta en referens till blob för att överföra en fil till en blobb i Målbehållaren. När du har en blobbreferens kan du överföra data till den med hjälp av [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Den här åtgärden skapas blobben om den inte redan finns eller skriver över den om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för överföring och hämtning, spara filen ska överföras som **fileAndPath** och namnet på blob i **filnamn**. I följande exempel överför filen till den behållare som kallas **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Det finns flera överför metoder som du kan använda med Blob storage. Till exempel om du har en dataström med minne, kan du använda metoden UploadFromStreamAsync i stället för UploadFromFileAsync. 

Blockblobbar kan vara alla slags textdata eller binära filen. Sidblobbar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Lägg till blobbar som används för inloggning, till exempel när du vill skriva till en fil och sedan hålla att lägga till mer information. De flesta objekt som lagras i Blob storage är blockblobbar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Du kan hämta en lista över filer i en behållare med hjälp av [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Följande kod hämtar listan över blobbar och loop genom dem, visar URI: er för de blobbar som påträffats. Du kan kopiera URI kommandofönstret och klistra in den i en webbläsare för att visa filen.

Om du har 5 000 eller färre blobbar i behållaren, hämtas alla blob-namn i ett anrop till ListBlobsSegmentedAsync. Om du har mer än 5 000 blobbar i behållaren, hämtar tjänsten i listan i uppsättningar med 5 000 tills alla blob-namn har hämtats. Så första gången detta API anropas returnerar de första 5 000 blob-namn och en fortsättningstoken. Den andra gången du ange token, hämtar tjänsten nästa uppsättning blobbnamnen och förrän fortsättningstoken är null, vilket anger att alla blob-namn har hämtats. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobbar till din lokala disk med hjälp av [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Följande kod hämtar blob som överförs i föregående avsnitt, att suffixet ”_DOWNLOADED” till blob-namn så att du kan se både filer på hårddisken. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du behöver inte längre blobbar på den här snabbstarten, kan du ta bort hela behållaren med hjälp av [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Också ta bort de filer som skapas om de inte längre behövs.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Nästa steg

I Snabbstart, du har lärt dig hur du överför filer mellan en lokal disk och Azure Blob storage med hjälp av .NET. Mer information om hur du arbetar med Blob storage fortsätta till Blob storage anvisningar.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-dotnet-how-to-use-blobs.md)

För ytterligare Azure Storage-kodexempel som du kan hämta och köra, se en lista över [Azure Storage-exempel som använder .NET](../common/storage-samples-dotnet.md).

Mer information om Lagringsutforskaren och Blobbar finns [hantera Azure-blobblagringsresurser med Lagringsutforskaren](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
