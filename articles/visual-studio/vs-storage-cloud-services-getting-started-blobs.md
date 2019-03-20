---
title: Kom igång med blob storage och Visual Studio-anslutna tjänster (molntjänster) | Microsoft Docs
description: Hur du kommer igång med Azure Blob-lagring i en cloud service-projekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 9f1ef06e0275954343c548d0f6937b9c6fbcfd18
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012129"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Kom igång med Azure Blob Storage och Visual Studio-anslutna tjänster (cloud services-projekt)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskrivs hur du kommer igång med Azure Blob Storage när du har skapat eller refererar till ett Azure Storage-konto med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan i en Visual Studio cloud services-projekt. Vi visar dig hur du skapa blob-behållare och hur du utför vanliga aktiviteter som att överföra, lista och ladda ned blobar. Exemplen är skrivna i C\# och använda den [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Azure Blob Storage är en tjänst för att lagra stora mängder Ostrukturerade data som kan nås från var som helst i världen via HTTP eller HTTPS. En enda blob kan vara valfri storlek. Blobar kan vara saker som bilder, ljud- och bildfiler, rådata och dokumentfiler.

Precis som filer live i mappar, lagringsblobar för Direktmigrering i behållare. När du har skapat ett lagringskonto kan skapa du en eller flera behållare i lagringen. Till exempel i en lagring som kallas ”klippboken”, kan du skapa behållare i lagringen som kallas ”avbildningar” för att lagra bilder och en annan kallas ”ljud” för att lagra ljudfiler. Du kan ladda upp enskilda blobfiler till dem när du har skapat behållarna.

* Läs mer på programmässigt hantera blobbar, [komma igång med Azure Blob storage med hjälp av .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).
* Allmän information om Azure Storage finns i [dokumentation om Storage](https://azure.microsoft.com/documentation/services/storage/).
* Allmän information om Azure Cloud Services finns i [dokumentation om Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/).
* Mer information om programmering i ASP.NET-program finns i [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Åtkomst till blob-behållare i kod
Om du vill komma åt blobar i molntjänstprojekt via programmering, måste du lägga till följande, om de inte redan finns.

1. Lägg till följande kod namnrymdsdeklarationer överst till en C#-fil som du vill komma åt Azure Storage via programmering.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta den din anslutningssträng för lagring och lagringskontoinformation från konfigurationen av Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
3. Hämta en **CloudBlobClient** objektet att referera till en befintlig behållare i ditt lagringskonto.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
4. Hämta en **CloudBlobContainer** objektet att referera till en specifik blob-behållare.
   
        // Get a reference to a container named "mycontainer."
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Använd all kod som visas i föregående procedur framför koden som visas i följande avsnitt.
> 
> 

## <a name="create-a-container-in-code"></a>Skapa en behållare i kod
> [!NOTE]
> Vissa API: er som utför anrop ut till Azure Storage i ASP.NET är asynkrona. Se [asynkron programmering med Async och Await](https://msdn.microsoft.com/library/hh191443.aspx) för mer information. Koden i följande exempel förutsätter att du använder async programmeringsmetoder.
> 
> 

Om du vill skapa en behållare i ditt storage-konto, allt du behöver göra är att lägga till ett anrop till **CreateIfNotExistsAsync** enligt följande:

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();


Du kan ange att behållaren ska vara offentlig med hjälp av följande kod för att filerna i behållaren tillgängliga för alla.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Alla på Internet kan se blobbar i en offentlig behållare, men du kan ändra eller ta bort dem bara om du har rätt åtkomstnyckel.

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container
Azure Storage stöder blockblobbar och sidblobbar. I de flesta fall är blockblobbar den rekommenderade typen.

Om du vill ladda upp en fil till en blockblob hämtar du en referens för containern och använder den för att hämta en referens för blockbloben. När du har en blobbreferens kan du ladda upp en dataström till den genom att anropa metoden **UploadFromStream**. Den här åtgärden skapar blobben om den inte redan fanns, eller skriver över den om den finns. Följande exempel visar hur du laddar upp en blob till en container och förutsätter att containern redan hade skapats.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container
Om du vill visa blobar i en container börjar du med att hämta en referens för containern. Sedan kan du använda containerns **ListBlobs**-metod för att hämta blobarna och/eller katalogerna i den. Komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **IListBlobItem**, måste du skicka den till en **CloudBlockBlob**, **CloudPageBlob**, eller  **CloudBlobDirectory** objekt. Om typen är okänd kan du använda en typkontroll för att avgöra till vilket objekt den ska skickas. Följande kod visar hur du hämtar och returnerar URI:n för varje objekt i **photos**-containern:

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

I ovanstående kodexempel visas den blobtjänsten har begreppet katalogerna i behållare, samt. Det här är så att du kan organisera dina blobar i en mer mapp-liknande struktur. Titta exempelvis på följande uppsättning blockblobar i en container med namnet **photos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

När du anropar **ListBlobs** på behållaren (som i föregående exempel), den samling som returneras innehåller **CloudBlobDirectory** och **CloudBlockBlob** objekt som representerar kataloger och BLOB-objekt som finns på den översta nivån. Här är den resulterande utdatan:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Om du vill kan du ange parametern **UseFlatBlobListing** för **ListBlobs**-metoden till **True**. Detta resulterar i varje blob som returneras som en **CloudBlockBlob**, oavsett directory. Här är anropet till **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

och här följer resultaten:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Mer information finns i [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Ladda ned blobbar
Om du vill ladda ned blobbar börjar du med att hämta en blobbreferens och anropar sedan **DownloadToStream**-metoden. I följande exempel används metoden **DownloadToStream** för att överföra blobbinnehållet till ett dataströmsobjekt som du sedan kan spara till en lokal fil.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Du kan också använda metoden **DownloadToStream** för att ladda ned innehållet i en blobb som en textsträng.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Ta bort blobbar
Ta bort en blob genom att först hämta en blobbreferens och anropar sedan den **ta bort** metod.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Visa en lista över blobbar på sidor asynkront
Om du visar ett stort antal blobbar, eller om du vill styra hur många resultat som returneras i samma åtgärd, kan du visa blobbar på resultatsidor. Det här exemplet illustrerar hur du returnerar resultat på sidor asynkront, så att körningen inte blockeras när ett stort antal resultat väntar på att returneras.

I det här exemplet returneras en platt blobblista, men du kan också visa en hierarkiskt ordnad lista genom att ange **useFlatBlobListing**-parametern för **ListBlobsSegmentedAsync**-metoden till **false**.

Eftersom exempelmetoden anropar en asynkron metod måste den föregås av nyckelordet **async** och returnera ett **Task**-objekt. Nyckelordet await som angetts för **ListBlobsSegmentedAsync**-metoden pausar körningen av exempelmetoden tills listuppgiften har slutförts.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

