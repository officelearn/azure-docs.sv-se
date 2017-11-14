---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av Java | Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av Java"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 2a825e59093b64ab56110f9a045b0325728b39b2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Överför objekt till/från Azure Blob storage med hjälp av Java

I den här snabbstarten att lära dig hur du använder Java att överföra, hämta och visa en lista med blockblobbar i en behållare i Azure Blob storage.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* Installera IDE-miljö som har Maven-integration

* Du kan också installera och konfigurera Maven ska fungera från kommandoraden

Den här kursen använder [Eclipse](http://www.eclipse.org/downloads/) med ”Eclipse IDE för Java-utvecklare” konfiguration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den [exempelprogram](https://github.com/Azure-Samples/storage-blobs-java-quickstart) används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) att hämta en kopia av programmet till din utvecklingsmiljö. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Det här kommandot klonar databasen till din lokala git-mapp. Öppna projektet genom att starta Eclipse och Stäng välkomstskärmen. Välj **filen** sedan **öppna projekt från filsystemet...** . Kontrollera att **identifiera och konfigurera projektet ordningarna** är markerad. Välj **Directory** gå sedan till där du sparade den klonade databasen i den väljer den **javaBlobsQuickstart** mapp. Kontrollera att den **javaBlobsQuickstarts** projektet visas som ett Eclipse-projekt sedan väljer **Slutför**.

När projektet har importerats, öppna **AzureApp.java** (finns i **blobQuickstart.blobAzureApp** inuti **src/main/java**), och Ersätt `accountname`och `accountkey` inuti den `storageConnectionString` sträng. Kör sedan programmet.
     

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
    
Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna den **AzureApp.Java** fil. Hitta de `storageConnectionString` variabeln. Ersätt den `AccountName` och `AccountKey` värdena i anslutningssträngen med de värden som du sparade från Azure-portalen. Din `storageConnectionString` bör likna följande:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Köra exemplet

Det här exemplet skapar en testfil i din standardkatalog (Mina dokument, för windows-användare), överför det till Blob storage, visar en lista över blobbar i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

Köra exemplet genom att trycka på **Ctrl + F11** i Eclipse.

Om du vill köra exemplet med Maven på kommandoraden, öppna ett gränssnitt och gå till **blobAzureApp** inuti klonade katalogen. Ange sedan `mvn compile exec:java`.

Följande är ett exempel på utdata om du kör programmet i Windows.

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 Kontrollera din standardkatalog (Mina dokument, för windows-användare) för de två filerna innan du fortsätter. Du kan öppna dem och de är identiska. Kopiera URL-Adressen för blob utanför konsolfönstret och klistra in den i en webbläsare för att visa innehållet i filen i Blob storage. När du trycker på Enter tas bort lagringsbehållare och filer.

Du kan också använda ett verktyg som den [Azure Lagringsutforskaren](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) att visa filer i Blob storage. Azure Lagringsutforskaren är ett kostnadsfritt plattformsoberoende verktyg som hjälper dig att komma åt kontoinformationen lagring. 

När du har kontrollerat filerna, tryck på RETUR för att slutföra demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör, öppna den **AzureApp.java** fil att titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Därefter går vi igenom exempelkoden så att du förstår hur det fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekt

Det första du gör är att skapa referenser till objekt som används för att komma åt och hantera Blob storage. De här objekten som bygger på varandra – varje används av den nästa i listan.

* Skapa en instans av den **CloudStorageAccount** objekt som pekar på den [lagringskonto](/java/api/com.microsoft.azure.management.storage._storage_account).

    Den **CloudStorageAccount** objektet är en representation av ditt lagringskonto och du kan ange och komma åt lagringskontoegenskaperna programmässigt. Med hjälp av den **CloudStorageAccount** objekt som du kan skapa en instans av den **CloudBlobClient**, vilket är nödvändigt för att få åtkomst till blob-tjänsten.

* Skapa en instans av den **CloudBlobClient** -objekt som pekar på den [Blob-tjänst](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) i ditt lagringskonto.

    Den **CloudBlobClient** ger dig en åtkomstpunkt för blob-tjänsten så att du kan ange och komma åt egenskaper för blob storage via programmering. Med hjälp av den **CloudBlobClient** du kan skapa en instans av den **CloudBlobContainer** -objektet, vilket krävs för att skapa behållare.

* Skapa en instans av den **CloudBlobContainer** objekt som representerar den [behållare](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) du ansluter till. Behållare för att organisera dina blobbar som du använder mappar på din dator för att ordna dina filer.    

    När du har den **CloudBlobContainer**, du kan skapa en instans av den **CloudBlockBlob** objekt som hänvisar till specifikt [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) i som du är intresserad av, och utföra en överföring, hämta, kopiera, etc. igen.

> [!IMPORTANT]
> Behållarnamn måste vara gemener. Se [namnge och referera till behållare, Blobbar och Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) för mer information om behållaren och blob-namn.

### <a name="create-a-container"></a>Skapa en behållare 

I det här avsnittet, skapa en instans av objekten, skapa en ny behållare och ange behörigheter för behållaren så att blobbarna är offentliga och kan nås med bara en URL. Behållaren kallas **quickstartblobs**. 

Det här exemplet används **CreateIfNotExists** eftersom vi vill skapa en ny behållare varje gång exemplet körs. I en produktionsmiljö där du använder samma behållare i ett program, det är bättre att bara anropa **CreateIfNotExists** när. Du kan också skapa behållaren i förväg så du inte behöver skapa i koden.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobbar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobbar är de vanligaste och som används i denna Snabbstart. 

Hämta en referens till blob för att överföra en fil till en blobb i Målbehållaren. När du har en blobbreferens kan du överföra data till den med hjälp av [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Den här åtgärden skapas blobben om den inte redan finns eller skriver över den om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för överföringen och ladda ned. Spara filen ska överföras som **källa** och namnet på blob i **blob**. I följande exempel överför filen till den behållare som kallas **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Det finns flera [överför metoder](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) som du kan använda med Blob storage. Till exempel om du har en sträng som kan du använda metoden UploadText i stället för metoden Överför. 

Blockblobbar kan vara alla slags textdata eller binära filen. Sidblobbar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Lägg till blobbar som används för inloggning, till exempel när du vill skriva till en fil och sedan hålla att lägga till mer information. De flesta objekt som lagras i Blob storage är blockblobbar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Du kan hämta en lista över filer i en behållare med hjälp av [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Följande kod hämtar listan över blobbar och loop genom dem, visar URI: er för de blobbar som påträffats. Du kan kopiera URI kommandofönstret och klistra in den i en webbläsare för att visa filen.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobbar till din lokala disk med hjälp av [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Följande kod hämtar blob som överförs i föregående avsnitt, att suffixet ”_DOWNLOADED” till blob-namn så att du kan se både filer på hårddisken. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du behöver inte längre blobbar på den här snabbstarten, kan du ta bort hela behållaren med hjälp av [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Detta tar även bort filerna i behållaren.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Nästa steg

I Snabbstart, du har lärt dig hur du överför filer mellan en lokal disk och Azure Blob storage med hjälp av Java. Mer information om hur du arbetar med Blob storage fortsätta till Blob storage anvisningar.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-java-how-to-use-blob-storage.md)

Mer information om Lagringsutforskaren och Blobbar finns [hantera Azure-blobblagringsresurser med Lagringsutforskaren](../../vs-azure-tools-storage-explorer-blobs.md).
