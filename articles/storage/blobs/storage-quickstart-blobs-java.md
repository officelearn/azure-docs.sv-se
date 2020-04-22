---
title: 'Snabbstart: Azure Blob lagringsbibliotek v12 - Java'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage client library version 12 för Java för att skapa en behållare och en blob i Blob (objekt) lagring. Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/27/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 15e0eaaeb407ad22220faa7d085eea805fe62679
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731349"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Snabbstart: Hantera blobbar med Java v12 SDK

I den här snabbstarten lär du dig hantera blobbar med hjälp av Java. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och arkivdata. Du överför, hämtar och listblobar och skapar och tar bort behållare.

[Api-referensdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [Mällkodpaket](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagringskonto](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).

> [!NOTE]
> Mer om du vill komma igång med den tidigare SDK-versionen finns i [Snabbstart: Hantera blobbar med Java v8 SDK](storage-quickstart-blobs-java-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Blob storage-klientbiblioteket v12 för Java.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Java-program med namnet *blob-quickstart-v12*.

1. I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du Maven för att skapa en ny konsolapp med namnet *blob-quickstart-v12*. Skriv följande **mvn** kommando för att skapa en "Hello world!" Java-projektet.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart \
                          -DartifactId=blob-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. Utdata från att generera projektet bör se ut ungefär så här:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. Skapa en *blob-quickstart-v12* annan katalog som kallas *data*. Det är här blob-datafilerna kommer att skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

Öppna *filen pom.xml* i textredigeraren. Lägg till följande beroendeelement i gruppen av beroenden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Navigera till katalogen */src/main/java/com/blobbar/quickstart*
1. Öppna *filen App.java* i redigeraren
1. Ta `System.out.println("Hello world!");` bort utdraget
1. Lägga `import` till direktiv

Här är koden:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande Java-klasser för att interagera med dessa resurser:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): `BlobServiceClient` Med klassen kan du manipulera Azure Storage-resurser och blob-behållare. Lagringskontot tillhandahåller namnområdet på den översta nivån för Blob-tjänsten.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): `BlobServiceClientBuilder` Klassen tillhandahåller ett flytande byggare-API som hjälper `BlobServiceClient` till att underlätta konfiguration och instansiering av objekt.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): `BlobContainerClient` Med klassen kan du manipulera Azure Storage-behållare och deras blobbar.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): `BlobClient` Klassen låter dig manipulera Azure Storage blobbar.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): `BlobItem` Klassen representerar enskilda blobbar `listBlobsFlat`som returneras från ett anrop till .

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitt hur du utför följande med Azure Blob storage-klientbiblioteket för Java:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa en lista över blobarna i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `Main` här koden i metoden:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde i behållarnamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa sedan en instans av klassen [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) och anropa sedan [metoden create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) för att faktiskt skapa behållaren i ditt lagringskonto.

Lägg till den här `Main` koden i slutet av metoden:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodavsnitt:

1. Skapar en textfil i *data* den lokala datakatalogen.
1. Hämtar en referens till ett [BlobClient-objekt](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) genom att anropa metoden [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) på behållaren från avsnittet [Skapa en behållare.](#create-a-container)
1. Överför den lokala textfilen till blobben genom att anropa metoden [uploadFromFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) Den här metoden skapar bloben om den inte redan finns, men kommer inte att skriva över den om den gör det.

Lägg till den här `Main` koden i slutet av metoden:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobbar i behållaren genom att anropa [metoden listBlobs.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) I det här fallet har bara en blob lagts till i behållaren, så listningsåtgärden returnerar bara den bloben.

Lägg till den här `Main` koden i slutet av metoden:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta den tidigare skapade blobben genom att anropa [metoden downloadToFile.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) Exempelkoden lägger till ett suffix av "DOWNLOAD" i filnamnet så att du kan se båda filerna i det lokala filsystemet.

Lägg till den här `Main` koden i slutet av metoden:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen har skapat genom att ta bort hela behållaren med [borttagningsmetoden.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) De lokala filer som skapas av appen tas också bort.

Appen pausar för användarindata genom att anropa `System.console().readLine()` innan blobben, behållaren och de lokala filerna tas bort. Detta är en bra chans att kontrollera att resurserna skapades korrekt, innan de tas bort.

Lägg till den här `Main` koden i slutet av metoden:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i den lokala mappen och överför den till Blob-lagring. I exemplet visas sedan blobbar i behållaren och hämtar filen med ett nytt namn så att du kan jämföra gamla och nya filer.

Navigera till katalogen som innehåller *filen pom.xml* och `mvn` kompilera projektet med hjälp av följande kommando.

```console
mvn compile
```

Bygg sedan paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Utdata för appen liknar följande exempel:

```output
Azure Blob storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensningsprocessen *data* kontrollerar du datamappen efter de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **Retur för** att ta bort testfilerna och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du laddar upp, laddar ned och listar blobbar med Java.

Om du vill visa exempelappar för Blob-lagring fortsätter du att:

> [!div class="nextstepaction"]
> [Azure Blob storage SDK v12 Java-exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Mer information finns i [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Om du vill ha självstudier, exempel, snabbstarter och annan dokumentation besöker du [Azure för Java-molnutvecklare](/azure/developer/java/).
