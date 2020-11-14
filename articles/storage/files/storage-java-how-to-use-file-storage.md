---
title: Utveckla för Azure Files med Java | Microsoft Docs
description: Lär dig hur du utvecklar Java-program och tjänster som använder Azure Files för att lagra fildata.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 977777aff4aa32bf6876e1d573970d71ec71584e
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629775"
---
# <a name="develop-for-azure-files-with-java"></a>Utveckla för Azure Files med Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här kursen visar grunderna i hur du använder Java för att utveckla program eller tjänster som använder Azure Files för att lagra fildata. I den här självstudien skapar vi ett konsol program och visar hur du utför grundläggande åtgärder med Java och Azure Files:

* Skapa och ta bort Azure-filresurser
* Skapa och ta bort kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, ladda ned och ta bort en fil

> [!Note]  
> Eftersom Azure Files kan nås via SMB, är det möjligt att skriva program som har åtkomst till Azure-filresursen med hjälp av Java-I/O-standardklasserna. Den här artikeln beskriver hur du skriver program som använder Azure Storage Java SDK, som använder [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) för att prata med Azure Files.

## <a name="create-a-java-application"></a>Skapa ett Java-program
För att bygga exemplen behöver du Java Development Kit (JDK) och [Azure Storage SDK för Java](https://github.com/Azure/azure-storage-java). Du bör också ha skapat ett Azure Storage-konto.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera ditt program så att det använder Azure Files
Om du vill använda API: er för Azure Storage lägger du till följande-instruktion överst i Java-filen där du vill komma åt lagrings tjänsten från.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage
Om du vill använda Azure Files måste du ansluta till ditt Azure Storage-konto. Det första steget är att konfigurera en anslutnings sträng som vi använder för att ansluta till ditt lagrings konto. Nu ska vi definiera en statisk variabel.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Ersätt your_storage_account_name och your_storage_account_key med de faktiska värdena för ditt lagrings konto.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Ansluta till ett Azure Storage-konto
Om du vill ansluta till ditt lagrings konto måste du använda **CloudStorageAccount** -objektet och skicka en anslutnings sträng till dess **parse** -metod.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. parse** genererar en InvalidKeyException så du måste lägga den i ett try/catch-block.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Alla filer och kataloger i Azure Files finns i en behållare som kallas för en **resurs**. Ditt lagrings konto kan ha så många resurser som din konto kapacitet tillåter. För att få åtkomst till en resurs och dess innehåll måste du använda en Azure Files-klient.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Med hjälp av Azure Files-klienten kan du hämta en referens till en resurs.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Skapa resursen genom att använda **createIfNotExists** -metoden för CloudFileShare-objektet.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

I det här läget innehåller **resursen** en referens till en resurs med namnet **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Ta bort en Azure-filresurs
Borttagning av en resurs görs genom att **deleteIfExists** -metoden anropas för ett CloudFileShare-objekt. Här är exempel koden som gör det.

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
Du kan också organisera lagringen genom att lägga till filer i under kataloger i stället för att använda dem i rot katalogen. Med Azure Files kan du skapa så många kataloger som ditt konto kommer att tillåta. Koden nedan skapar en under katalog med namnet **sampledir** under rot katalogen.

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
Att ta bort en katalog är en enkel uppgift, men det bör noteras att du inte kan ta bort en katalog som fortfarande innehåller filer eller andra kataloger.

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
Att hämta en lista över filer och kataloger i en resurs görs enkelt genom att anropa **listFilesAndDirectories** på en CloudFileDirectory-referens. Metoden returnerar en lista med ListFileItem-objekt som du kan iterera på. Som exempel visar följande kod filer och kataloger i rot katalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Ladda upp en fil
I det här avsnittet får du lära dig hur du laddar upp en fil från lokal lagring till rot katalogen för en resurs.

Det första steget när du laddar upp en fil är att hämta en referens till den katalog där den ska finnas. Det gör du genom att anropa **getRootDirectoryReference** -metoden för Share-objektet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu när du har en referens till resursens rot Katalog kan du ladda upp en fil till den med hjälp av följande kod.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Ladda ned en fil
En av de vanligaste åtgärderna som du utför för Azure Files är att ladda ned filer. I följande exempel hämtar koden SampleFile.txt och visar dess innehåll.

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
En annan vanlig Azure Files åtgärd är fil borttagning. Följande kod tar bort en fil med namnet SampleFile.txt som lagras i en katalog med namnet **sampledir**.

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
Om du vill veta mer om andra Azure Storage-API: er, följer du dessa länkar.

* [Azure för Java-utvecklare](/java/azure)/)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK för Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK-referens](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API för Azure Storage Services](/rest/api/storageservices/)
* [Azure Storage teamets blogg](/archive/blogs/windowsazurestorage/)
* [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy-v10.md)
* [Felsökning av problem i Azure Files – Windows](storage-troubleshoot-windows-file-connection-problems.md)