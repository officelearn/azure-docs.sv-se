---
title: "Snabbstart för Azure – Överföra objekt till och från Azure Blob Storage med hjälp av .NET | Microsoft Docs"
description: "Lär dig snabbt hur du överför objekt till och från Azure Blob Storage med hjälp av .NET"
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
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Överföra objekt till och från Azure Blob Storage med hjälp av .NET

I den här snabbstarten får du lära dig att använda .NET-klientbiblioteket för Azure Storage för att ladda upp, ladda ned och lista blockblobar i en behållare.

## <a name="prerequisites"></a>Krav

Du slutför den här snabbstarten genom att installera [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) med följande arbetsbelastning:

- **Azure Development**

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet som används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösningen genom att leta reda på mappen storage-blobs-dotnet-quickstart, öppna den och dubbelklicka på storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna filen `app.config` från Solution Explorer i Visual Studio. Leta reda på posten `StorageConnectionString`. För **värde** ersätter du hela anslutningssträngens värde med det du sparade från Azure-portalen. `storageConnectionString` bör se ut ungefär så här:

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

## <a name="run-the-sample"></a>Kör exemplet

Det här exemplet skapar en testfil i Mina dokument, överför den till Blob Storage, listar blobarna i behållaren och laddar sedan ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

Kör exemplet genom att trycka på F5. Då visar resultatet i ett konsolfönster som liknar följande: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

När du trycker på valfri tangent för att fortsätta tas lagringsbehållaren och filerna bort. Kontrollera att de två filerna finns i MyDocuments innan du fortsätter. Du kan öppna dem och se att de är identiska. Kopiera URL:en för bloben från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i filen i bloblagringen.

Du kan också använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen Program.cs och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra – vart och ett används av nästa i listan.

* Skapa en instans av objektet **CloudStorageAccount** som pekar mot lagringskontot. 

* Skapa en instans av objektet **CloudBlobClient**, som pekar mot blobtjänsten i lagringskontot. 

* Skapa en instans av objektet **CloudBlobContainer**, som representerar den behållare du får åtkomst till. Behållare används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har **CloudBlobContainer** kan du skapa en instans av objektet **CloudBlockBlob** som pekar mot den specifika blob du är intresserad av, och utföra åtgärder som att ladda upp, ladda ned och kopiera.

> [!IMPORTANT]
> Behållarnamn måste använda gemener. Mer information om behållare och blobnamn finns i [Namngivning och referens av behållare, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du en instans av objekt, skapar en ny behållare och anger sedan behörigheter för behållaren så att blobarna är offentliga och går att komma åt med bara en URL. Behållaren heter **quickstartblobs**. 

Det här exemplet använder **CreateIfNotExists** eftersom vi vill skapa en ny behållare varje gång exemplet körs. I en produktionsmiljö där du använder samma behållare i ett program är det bättre att bara anropa **CreateIfNotExists** en gång. Du kan också skapa behållaren i förväg så att du inte behöver skapa den i koden.

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

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart. 

Hämta en referens till bloben i målbehållaren om du vill överföra en fil till en blob. När du har blobreferensen kan du ladda upp data till den med hjälp av [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över den om den finns.

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar filen som ska laddas upp som **fileAndPath** och namnet på bloben i **localFileName**. I följande exempel överförs filen till behållaren med namnet **quickstartblobs**.

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

Det går att använda flera uppladdningsmetoder med Blob Storage. Om du till exempel har en minnesström kan du använda metoden UploadFromStreamAsync i stället för UploadFromFileAsync. 

Blockblobar kan vara valfri texttyp eller binär fil. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Du kan hämta en lista över filer i behållaren med hjälp av [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). Följande kod hämtar listan över blobar och går sedan igenom dem och visar URI för de blobar som har påträffats. Du kan kopiera URI från kommandofönstret och klistra in den i en webbläsare om du vill visa filen.

Om du har 5 000 eller färre blobar i behållaren hämtas alla blobnamn i ett anrop till ListBlobsSegmentedAsync. Om du har fler än 5 000 blobar i behållaren hämtar tjänsten listan i uppsättningar om 5 000 tills alla blobnamn har hämtats. Första gången denna API anropas returnerar den därför de första 5 000 blobnamnen och en fortsättningstoken. Den andra gången anger du token och tjänsten hämtar nästa uppsättning med blobnamn och så vidare tills fortsättningstoken är null, vilket visar att alla blobnamn har hämtats. 

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

Ladda ned blobar till den lokala disken med hjälp av [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

Följande kod laddar ned bloben som överfördes i föregående avsnitt och lägger till ett suffix med "_DOWNLOADED" i blobnamnet så att du kan se båda filerna på den lokala disken. 

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

Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela behållaren med hjälp av [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Ta även bort de filer som skapades om de inte längre behövs.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med .NET. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-dotnet-how-to-use-blobs.md)

Fler Azure Storage-kodexempel som du kan ladda ned och köra finns i vår lista med [Azure Storage-exempel som använder .NET](../common/storage-samples-dotnet.md).

Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
