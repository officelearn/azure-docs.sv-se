---
title: Utveckla för Azure-filer med Java | Microsoft-dokument
description: Lär dig hur du utvecklar Java-program och -tjänster som använder Azure-filer för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 28a280ea7c3bf9ef84a1fff05da5090ed526fb12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837459"
---
# <a name="develop-for-azure-files-with-java"></a>Utveckla för Azure Files med Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här självstudien visar grunderna i att använda Java för att utveckla program eller tjänster som använder Azure Files för att lagra fildata. I den här självstudien skapar vi ett konsolprogram och visar hur du utför grundläggande åtgärder med Java- och Azure-filer:

* Skapa och ta bort Azure-filresurser
* Skapa och ta bort kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, ladda ned och ta bort en fil

> [!Note]  
> Eftersom Azure-filer kan nås via SMB är det möjligt att skriva program som kommer åt Azure-filresursen med hjälp av de vanliga Java I/O-klasserna. I den här artikeln beskrivs hur du skriver program som använder Azure Storage Java SDK, som använder [AZURE Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) för att prata med Azure-filer.

## <a name="create-a-java-application"></a>Skapa ett Java-program
För att skapa exemplen behöver du Java Development Kit (JDK) och [Azure Storage SDK för Java](https://github.com/Azure/azure-storage-java). Du bör också ha skapat ett Azure-lagringskonto.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files
Om du vill använda Azure-lagrings-API:erna lägger du till följande uttryck till toppen av Java-filen där du tänker komma åt lagringstjänsten från.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure-lagringsanslutningssträng
Om du vill använda Azure-filer måste du ansluta till ditt Azure-lagringskonto. Det första steget är att konfigurera en anslutningssträng som vi använder för att ansluta till ditt lagringskonto. Låt oss definiera en statisk variabel för att göra det.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Ersätt your_storage_account_name och your_storage_account_key med de faktiska värdena för ditt lagringskonto.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Ansluta till ett Azure-lagringskonto
Om du vill ansluta till ditt lagringskonto måste du använda **CloudStorageAccount-objektet** och skicka en anslutningssträng till dess **tolkningsmetod.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** kastar en InvalidKeyException så att du måste lägga den i ett try/catch-block.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Alla filer och kataloger i Azure Files finns i en behållare som kallas **en Resurs**. Ditt lagringskonto kan ha så många resurser som din kontokapacitet tillåter. Om du vill få åtkomst till en resurs och dess innehåll måste du använda en Azure Files-klient.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Med hjälp av Azure Files-klienten kan du sedan hämta en referens till en resurs.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Om du vill skapa resursen använder du metoden **createIfNotExists** för CloudFileShare-objektet.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Nu innehåller **aktien** en referens till en resurs med namnet **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Ta bort en Azure-filresurs
Ta bort en resurs görs genom att anropa **metoden deleteIfExists** på ett CloudFileShare-objekt. Här är exempelkod som gör det.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Skapa en katalog
Du kan också ordna lagring genom att placera filer i underkataloger i stället för att ha alla i rotkatalogen. Med Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan skapar en underkatalog med namnet **Sampledir** under rotkatalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Ta bort en katalog
Att ta bort en katalog är en enkel uppgift, även om det bör noteras att du inte kan ta bort en katalog som fortfarande innehåller filer eller andra kataloger.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs
Att få en lista över filer och kataloger i en resurs görs enkelt genom att anropa **listFilesAndDirectories** på en CloudFileDirectory-referens. Metoden returnerar en lista över ListFileItem-objekt som du kan iterera på. Som ett exempel kommer följande kod att lista filer och kataloger i rotkatalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Överför en fil
I det här avsnittet får du lära dig hur du laddar upp en fil från lokalt lagringsutrymme till rotkatalogen för en resurs.

Det första steget i att ladda upp en fil är att få en referens till katalogen där den ska finnas. Du gör detta genom att anropa **metoden getRootDirectoryReference** för resursobjektet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu när du har en referens till resursens rotkatalog kan du ladda upp en fil till den med hjälp av följande kod.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Hämta en fil
En av de vanligaste åtgärderna du kommer att utföra mot Azure Files är att hämta filer. I följande exempel hämtar koden SampleFile.txt och dess innehåll visas.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Ta bort en fil
En annan vanlig Azure Files-åtgärd är borttagning av filer. Följande kod tar bort en fil med namnet SampleFile.txt som lagras i en katalog med namnet **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om andra Azure-lagrings-API:er följer du dessa länkar.

* [Azure för Java-utvecklare](/java/azure)/)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK för Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK-referens](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogg för Azure Storage Team](https://blogs.msdn.com/b/windowsazurestorage/)
* [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md)
* [Felsökning av problem i Azure Files – Windows](storage-troubleshoot-windows-file-connection-problems.md)
