---
title: Utveckla för Azure Files med Java | Microsoft Docs
description: Lär dig hur du utvecklar Java-program och tjänster som använder Azure-filer för att lagra fildata.
services: storage
author: wmgries
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.component: files
ms.openlocfilehash: aa63a31f7f84502a29aad6b38f454ea1080127e0
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39531317"
---
# <a name="develop-for-azure-files-with-java"></a>Utveckla för Azure Files med Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Om den här självstudiekursen
Den här kursen visar grunderna i att använda Java för att utveckla program eller tjänster som använder Azure-filer för att lagra fildata. I den här självstudien ska vi skapa ett konsolprogram och visar hur du utför grundläggande åtgärder med Java och Azure Files:

* Skapa och ta bort Azure-filresurser
* Skapa och ta bort kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, hämta och ta bort en fil

> [!Note]  
> Eftersom Azure Files kan nås över SMB, är det möjligt att skriva program som har åtkomst till Azure-filresursen med Java i/o-standardklasser. Den här artikeln beskriver hur du skriver program som använder Azure Storage Java SDK, som använder den [API: et för Azure Files-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) att kommunicera med Azure Files.

## <a name="create-a-java-application"></a>Skapa ett Java-program
Om du vill skapa exemplen måste Java Development Kit (JDK) och [Azure Storage SDK för Java](https://github.com/Azure/azure-storage-java). Du bör också skapat ett Azure storage-konto.

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera programmet att använda Azure Files
För att använda API: er för Azure storage, lägger du till följande uttryck överst i Java-filen där du vill få åtkomst till lagringstjänsten.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutningssträng för Azure storage
Om du vill använda Azure Files, måste du ansluta till Azure storage-kontot. Det första steget är att konfigurera en anslutningssträng som vi använder för att ansluta till ditt lagringskonto. Nu ska vi definiera en statisk variabel för att göra detta.

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

## <a name="connecting-to-an-azure-storage-account"></a>Ansluta till ett Azure storage-konto
Om du vill ansluta till ditt storage-konto, måste du använda den **CloudStorageAccount** objekt som passerar en anslutningssträng för dess **parsa** metod.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** utlöser en InvalidKeyException så måste du placera det i ett try-/ catch-block.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Alla filer och kataloger i Azure Files finns i en behållare som kallas en **resursen**. Ditt storage-konto kan ha så många resurser som gör att din kapacitet. För att få åtkomst till en resurs och dess innehåll kan behöva du använda en Azure Files-klient.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Med hjälp av Azure Files-klienten, kan du sedan hämta en referens till en resurs.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Om du vill skapa resursen, Använd den **createIfNotExists** -metoden i CloudFileShare-objektet.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Nu **dela** innehåller en referens till en resurs med namnet **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Ta bort en Azure-filresurs
Tar bort en resurs gör du genom att anropa den **deleteIfExists** metod på ett CloudFileShare-objekt. Här är exempelkod som gör detta.

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
Du kan även sortera lagring genom att placera filer i underkataloger i stället för att alla i rotkatalogen. Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan skapar en underkatalog med namnet **sampledir** under rotkatalogen.

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
Ta bort en katalog är en enkel uppgift, men det bör noteras att du inte kan ta bort en katalog som fortfarande innehåller filer eller andra kataloger.

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
Hämtar en lista över filer och kataloger i en resurs görs enkelt genom att anropa **listFilesAndDirectories** på en CloudFileDirectory referens. Metoden returnerar en lista över ListFileItem-objekt som du kan iterera på. Till exempel följande kod visar en lista över filer och kataloger i rotkatalogen.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Överför en fil
I det här avsnittet lär du dig att överföra en fil från lokal lagring till rotkatalogen för en resurs.

Det första steget i att ladda upp en fil är att hämta en referens till katalogen där det ska finnas. Du gör detta genom att anropa den **getRootDirectoryReference** -metoden i resurs-objektet.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Nu när du har en referens till rotkatalogen för resursen kan överföra du en fil till den med hjälp av följande kod.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Hämta en fil
En av de mer frekventa åtgärder som du utför mot Azure Files är att hämta filer. I följande exempel koden hämtar SampleFile.txt och dess innehåll visas.

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
En annan vanliga Azure Files-åtgärden kan ta bort filen. Följande kod tar du bort en fil med namnet SampleFile.txt som lagrats i en katalog med namnet **sampledir**.

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
Om du vill lära dig mer om API: er för andra Azure storage kan du följa dessa länkar.

* [Azure för Java-utvecklare](/java/azure)/)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK för Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK-referens](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage/)
* [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md)
* [Felsökning av problem i Azure Files – Windows](storage-troubleshoot-windows-file-connection-problems.md)