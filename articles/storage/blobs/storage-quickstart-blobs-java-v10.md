---
title: 'Azure snabbstart: Skapa en blob i objektlagring med hjälp av Java Storage SDK V10 | Microsoft Docs'
description: I den här snabbstarten skapar du en container i objektlagring (Azure Blob), laddar upp en fil, visar en lista med objekt och laddar ned med hjälp av Java Storage SDK.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: rogarana
ms.openlocfilehash: c675dd17994abaaf6d0eed1934bec8f2220e7435
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955716"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Snabbstart: Ladda upp, ladda ned och visa blobar med Java Storage SDK V10

I den här snabbstarten får du lära dig hur du använder nya Java Storage SDK för att ladda upp, ladda ned och visa blockblobar i en container i Azure Blob Storage. Detta nya Java-SDK använder den reaktiva programmeringsmodellen med RxJava som gör att du kan använda asynkrona åtgärder. Mer information om RxJava: [reaktiva tillägg för Java VM](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>Nödvändiga komponenter

Installera och konfigurera dessa program:

* [Maven](http://maven.apache.org/download.cgi) kan arbeta från kommandoraden eller valfri integrerad utvecklingsmiljö för Java som du föredrar
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) som används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) för att hämta en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen.

När projektet har importerats öppnar du **Quickstart.java**, som finns i **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
Den här lösningen kräver att du lagrar namnet och nyckeln för ditt lagringskonto på ett säkert sätt. Lagra dem i miljövariabler som är lokala på den dator som kör exemplet. Följ antingen Linux- eller Windows-exemplet, beroende på operativsystemet, för att skapa miljövariablerna.

### <a name="linux-example"></a>Linux-exemplet

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windows-exempel

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Kör exemplet

Det här exemplet skapar en testfil i din standardkatalog, **AppData\Local\Temp** för Windows-användare. Sedan uppmanas du att vidta följande steg:

1. Ange kommandon för att ladda upp testfilen till Azure Blob-lagring.
2. Visa en lista över blobarna i containern.
3. Ladda ned den uppladdade filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

Om du vill köra exemplet med hjälp av Maven på kommandoraden öppnar du ett gränssnitt och går till **storage-blobs-java-v10-quickstart** i den klonade katalogen. Ange sedan `mvn compile exec:java`.

Det här exemplet visar dina utdata om du kör programmet på Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Du kontrollerar exemplet, så ange kommandon så att den kör koden. Inmatningarna är skiftlägeskänsliga.

Du kan även använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som du kan använda för att komma åt information på lagringskontot. 

Verifiera filerna. Välj sedan **E** och välj **Retur** för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen **Quickstart.java** och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

I följande avsnitt går vi igenom exempelkoden så att du förstår hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Först skapar du referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra. Varje objekt används av nästa i listan.

1. Skapa en instans av objektet **StorageURL** som pekar på lagringskontot.

    * Objektet [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) är en representation av ditt lagringskonto. Du kan använda det för att generera en ny pipeline. 
    * En pipeline är en uppsättning principer som används för att manipulera begäranden och svar med metoder för autentisering, inloggning och återförsök. Mer information finns i [HTTP-pipeline](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Med hjälp av pipelinen skapar du en instans av objektet [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Med hjälp av objektet **ServiceURL** skapar du en instans av [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * **ContainerURL** krävs för att köra åtgärder på blob-behållare.

2. Skapa en instans av objektet **ContainerURL** som representerar den container du får åtkomst till. Containrar organiserar dina blobar på samma sätt som mappar organiserar filer på datorn.

    * **ContainerURL** ger en åtkomstpunkt till containertjänsten. 
    * Du kan skapa en instans av objektet [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable) genom att använda [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * **BlobURL** krävs för att skapa blobar.

3. Skapa en instans av objektet **BlobURL** som pekar på den specifika blob som du är intresserad av. 

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar och blobnamn finns i [Namngivning och referens av containrar, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Skapa en container 

I det här avsnittet skapar du en instans av **ContainerURL**. Du skapar även en ny container. Containern i exemplet heter **quickstart**. 

Det här exemplet använder [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_) så att du kan skapa en ny container varje gång exemplet körs. Du kan även skapa containern i förväg så att du inte behöver skapa den i koden.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är de vanligaste. De används i den här snabbstarten. 

1. Hämta en referens till bloben i målcontainern om du vill överföra en fil till en blob. 
2. När du får blobreferensen kan du ladda upp en fil till den med någon av följande API:er:

    * Lågnivå-API:er. Exempel är [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), som även kallas PutBlob, och [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), som även kallas PutBLock, i instansen för **BlockBlobURL**. 

    * Högnivå-API:er finns i [TransferManager-klassen](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Ett exempel är metoden [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

    Den här åtgärden skapar bloben om den inte redan finns. Den skriver över bloben om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för överföring och nedladdning. Den lagrar filer som ska laddas upp som **sourceFile** och delar URL med bloben i **blob**. I följande exempel laddas filen upp till containern med namnet **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Blockblobar kan vara valfri texttyp eller binär fil. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för att lägga till data i slutet, och de används ofta för loggning. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Du kan hämta en lista med objekt i en container med hjälp av [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Den här metoden returnerar upp till 5 000 objekt på samma gång tillsammans med en fortsättningsmarkör, eller nästa-markör, om det finns fler att lista i containern. Skapa en hjälpfunktion som anropar sig själv upprepade gånger när det finns en nästa-markör i föregående **listBlobsFlatSegment**-svar.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobar till din lokala disk med hjälp av [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

Följande kod laddar ned bloben som överfördes i föregående avsnitt. Den lägger till suffixet **_DOWNLOADED** till blobnamnet så att du kan se båda filerna på den lokala disken. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de blobar som laddades upp i den här snabbstarten kan du ta bort hela containern med hjälp av [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Denna metod tar även bort filerna i containern.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med hjälp av Java. 

> [!div class="nextstepaction"]
> [Storage SDK V10 för Java-källkod](https://github.com/Azure/azure-storage-java/)
> [API-referens](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [Läs mer om RxJava](https://github.com/ReactiveX/RxJava)
