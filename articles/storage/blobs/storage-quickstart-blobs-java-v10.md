---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Java Storage SDK V10 | Microsoft Docs
description: I den här snabbstarten skapar du en container i objektlagring (Blob), laddar upp en fil, visar en lista med objekt och laddar ned med hjälp av Java Storage SDK.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704478"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>Snabbstart: Ladda upp, ladda ned och visa blobar med Java SDK V10 (förhandsversion)

I den här snabbstarten får du lära dig hur du använder nya Java Storage SDK för att ladda upp, ladda ned och visa blockblobar i en container i Azure Blob Storage. Detta nya Java-SDK använder den reaktiva programmeringsmodellen med RxJava som gör att du kan använda asynkrona åtgärder. Läs mer om RxJava [här](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

* Du kan också installera och konfigurera [Maven](http://maven.apache.org/download.cgi) så att det fungerar från kommandoraden, eller någon annan valfri Java IDE
* Installera och konfigurera [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) som används i den här snabbstarten är ett grundläggande konsolprogram. 

Använd [git](https://git-scm.com/) för att hämta en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen.

När projektet har importerats öppnar du **Quickstart.java** (finns i **src/main/java/quickstart**).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
Den här lösningen kräver att ditt lagringskontonamn och nyckel är säkert lagrade i miljövariabler som ligger lokalt på datorn som kör exemplet. Följ något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

### <a name="for-linux"></a>För Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>För Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Kör exemplet

Det här exemplet skapar en testfil i din standardkatalog (AppData\Local\Temp för Windows-användare), ber dig ange kommandon för att överföra testfilen till bloblagringen, listar blobarna i containern och hämtar den uppladdade filen med ett nytt namn så att du kan jämföra de gamla och nya filerna. 

Om du vill köra exemplet med Maven på kommandoraden kan du öppna ett gränssnitt och gå till **storage-blobs-java-v10-quickstart** i den klonade katalogen. Ange sedan `mvn compile exec:java`.

Följande är ett exempel på utdata om du kör programmet i Windows.

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

Du har kontroll över exemplet, så ange kommandon för att köra koden. Tänk på att indata är skiftlägeskänsliga.

Du kan också använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna, **E** och tryck på Retur för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen **Quickstart.java** och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra – vart och ett används av nästa i listan.

* Skapa en instans av objektet StorageURL som pekar på lagringskontot.

    Objektet [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) är en representation av ditt lagringskonto och gör att du kan generera en ny pipeline. En pipeline ([HTTP-pipeline](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)) är en uppsättning principer som används för att manipulera begäranden och svar med metoder för autentisering, inloggning och återförsök. Med pipelinen kan du skapa en instans av objektet [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview) så att du kan skapa en instans av [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), som krävs för att köra åtgärder för blobcontainrar.

* Skapa en instans av objektet ContainerURL, som representerar den container du får åtkomst till. Behållare används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

    **ContainerURL** ger dig en åtkomstpunkt till containertjänsten. Med hjälp av **ContainerURL** kan du skapa en instans av objektet [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), som krävs för att skapa blobar.

* Skapa en instans av objektet [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview), som pekar på den specifika blob som du är intresserad av.

> [!IMPORTANT]
> Behållarnamn måste använda gemener. Mer information om behållare och blobnamn finns i [Namngivning och referens av behållare, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Skapa en behållare 

I det här avsnittet skapar du en instans av ContainerURL och en ny container. Containern i exemplet heter **quickstart**. 

Det här exemplet använder [containerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview) eftersom vi vill skapa en ny container varje gång exemplet körs. Du kan också skapa behållaren i förväg så att du inte behöver skapa den i koden.

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

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart. 

Hämta en referens till bloben i målbehållaren om du vill överföra en fil till en blob. När du har blobreferensen kan du överföra en fil till den med hjälp av API:er på låg nivå, till exempel [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview) (även kallat PutBlob) eller [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_) (även kallat PutBLock) i instansen av BlockBlobURL, eller med hjälp av API:er på hög nivå som finns i klassen [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview), till exempel metoden [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över den om den finns.

Exempelkoden skapar en lokal fil som ska användas för överföring och nedladdning. Filen som ska överföras lagras som **sourceFile** och URL:en för bloben lagras i **blob**. I följande exempel laddas filen upp till containern med namnet **quickstart**.

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

Blockblobar kan vara valfri texttyp eller binär fil. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för att lägga till data i slutet, och används ofta för loggning. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Du kan hämta en lista med objekt i en container med hjälp av [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview). Den här metoden returnerar upp till 5 000 objekt på samma gång tillsammans med en fortsättningsmarkör (nästa-markör) om det finns fler listobjekt i containern. Vi har skapat en hjälpfunktion som anropar sig själv upprepade gånger så länge det finns en nästa-markör i föregående listBlobsFlatSegment-svar.

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

Ladda ned blobar till din lokala disk med hjälp av [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview).

Följande kod laddar ned bloben som överfördes i föregående avsnitt och lägger till ett suffix med "_DOWNLOADED" i blobnamnet så att du kan se båda filerna på den lokala disken. 

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

Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela containern med hjälp av [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview). Denna metod tar även bort filerna i containern.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Java. 

> [!div class="nextstepaction"]
> [Storage SDK V10 för Java-källkod](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [API-referens](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [Läs mer om RxJava](https://github.com/ReactiveX/RxJava)