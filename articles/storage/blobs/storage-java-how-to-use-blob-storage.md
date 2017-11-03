---
title: "Använda Azure Blob storage (objektlagring) från Java | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: java
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 2e223b38-92de-4c2f-9254-346374545d32
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 91ef09916dbb587305572ea640fb4408ea9aebb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-java"></a>Använda Blob Storage från Java
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Översikt
Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här artikeln visar hur du utför vanliga scenarier med hjälp av Microsoft Azure Blob storage. Exemplen är skrivna i Java och Använd den [Azure Storage SDK för Java][Azure Storage SDK for Java]. Scenarier som tas upp inkluderar **överför**, **lista**, **hämtar**, och **bort** blobbar. Mer information om blobbar finns i [nästa steg](#Next-Steps) avsnitt.

> [!NOTE]
> En SDK är tillgänglig för utvecklare som använder Azure Storage på Android-enheter. Mer information finns i [Azure Storage SDK för Android][Azure Storage SDK for Android].
>
>

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Skapa ett Java-program
I den här artikeln använder lagringsfunktioner som kan köras i en Java-program lokalt eller i kod som körs i en webbroll eller worker-rollen i Azure.

Om du vill göra det, behöver du installera Java Development Kit (JDK) och skapa ett Azure Storage-konto i din Azure-prenumeration. När du har gjort det, behöver du kontrollera att utvecklingssystemet uppfyller minsta krav och beroenden som visas i den [Azure Storage SDK för Java] [ Azure Storage SDK for Java] databasen på GitHub. Om datorn uppfyller dessa krav, kan du följa instruktionerna för att hämta och installera Azure Storage-biblioteken för Java på datorn från den lagringsplatsen. När du har slutfört uppgifterna, kommer du att kunna skapa ett Java-program som använder exemplen i den här artikeln.

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurera ditt program för att få åtkomst till Blob storage
Lägg till följande importuttryck överst i Java-filen där du vill använda Azure Storage-API: er för att få tillgång till blobbar.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Ställ in en anslutningssträng för Azure Storage
Ett Azure Storage-klienten använder en anslutningssträng för lagring för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till data management services. När den körs i ett klientprogram, du måste ange anslutningssträngen för lagring i följande format, med hjälp av namnet på ditt lagringskonto och den primära åtkomstnyckeln för lagringskontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. I följande exempel visas hur du kan deklarera statiska fält för anslutningssträngen.

```java
// Define the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

I ett program som körs inom en roll i Microsoft Azure, kan den här strängen lagras i konfigurationsfilen service *ServiceConfiguration.cscfg*, och kan nås med ett anrop till den **RoleEnvironment.getConfigurationSettings** metod. I följande exempel hämtas anslutningssträng från en **inställningen** element med namnet *StorageConnectionString* i tjänstekonfigurationsfilen.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Följande exempel förutsätter att du har använt ett av dessa två sätt för att hämta anslutningssträngen för lagring.

## <a name="create-a-container"></a>Skapa en behållare
En **CloudBlobClient** objekt kan du hämta referensobjekt för behållare och blobbar. Följande kod skapar en **CloudBlobClient** objekt.

> [!NOTE]
> Det finns flera sätt att skapa **CloudStorageAccount** objekt; mer information finns i **CloudStorageAccount** i den [referens för Azure Storage Client SDK].
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Använd den **CloudBlobClient** objekt för att hämta en referens till den behållare som du vill använda. Du kan skapa behållaren om den inte finns med i **createIfNotExists** metod som annars returnerar befintlig behållare. Som standard är den nya behållaren privat, så du måste ange din lagringsåtkomstnyckel (som du gjorde tidigare) för att ladda ned blobbar från den här behållaren.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Get a reference to a container.
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist.
    container.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

### <a name="optional-configure-a-container-for-public-access"></a>Valfritt: Konfigurera en behållare för allmän åtkomst
Behörigheter för en behållare som är konfigurerade för privat åtkomst som standard, men du kan enkelt konfigurera en behållare behörigheter för att tillåta offentlig, skrivskyddad åtkomst för alla användare på Internet:

```java
// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Om du vill överföra en fil till en blob, hämta en referens för behållaren och använda den för att hämta en blobbreferens. När du har en blobbreferens kan du ladda upp en dataström genom att anropa överför för blobbreferens. Den här åtgärden skapas blobben om den inte finns eller skriva över den om så inte. Följande kodexempel visar detta och förutsätter att behållaren redan har skapats.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Define the path to a local file.
    final String filePath = "C:\\myimages\\myimage.jpg";

    // Create or overwrite the "myimage.jpg" blob with contents from a local file.
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File(filePath);
    blob.upload(new FileInputStream(source), source.length());
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa blobbar i en behållare, att hämta en referens för behållaren som du gjorde för att ladda upp en blob. Du kan använda behållarens **listBlobs** metod med en **för** loop. Följande kod visar Uri för varje blobb i en behållare i konsolen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them.
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Observera att du namnge blobbar med sökvägsinformation i deras namn. På så sätt skapar du en virtuell katalogstruktur som du kan organisera och bläddra i precis som i ett traditionellt filsystem. Observera att katalogstrukturen bara är virtuell – de enda tillgängliga resurserna i Blob Storage är behållare och blobbar. Men klientbiblioteket erbjuder en **CloudBlobDirectory** objektet att referera till en virtuell katalog och förenkla arbetet med blobbar som är ordnade på det här sättet.

Du kan till exempel ha en behållare med namnet ”foton”, där du kan ladda upp blobbar med namnet ”rootphoto1”, ”2010/photo1”, ”2010/photo2” och ”2011/photo1”. Då skapas de virtuella katalogerna ”2010” och ”2011” i ”foton”-behållaren. När du anropar **listBlobs** ”foton”-behållaren innehåller samlingen returnerade **CloudBlobDirectory** och **CloudBlob** objekt som representerar kataloger och blobbar som finns på den översta nivån. I det här fallet returneras ”2010” och ”2011”-kataloger, samt foto ”rootphoto1”. Du kan använda den **instanceof** operatorn för att skilja dessa objekt.

Alternativt kan du överföra i parametrar för att den **listBlobs** metod med den **useFlatBlobListing** parametern inställd på true. Detta resulterar i varje blob som returneras oavsett directory. Mer information finns i **CloudBlobContainer.listBlobs** i den [referens för Azure Storage Client SDK].

## <a name="download-a-blob"></a>Ladda ned en blob
Följ samma steg för att ladda ned blobbar som du gjorde för att överföra en blob för att hämta en blobbreferens. I exemplet överför anropa överför för blob-objektet. I följande exempel anropar hämta för att överföra blobbinnehållet till ett stream-objektet som en **FileOutputStream** som du kan använda för att bevara blob till en lokal fil.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop through each blob item in the container.
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory.
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name.
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob"></a>Ta bort en blob
Ta bort en blob och hämta en blobbreferens anropet **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg".
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob.
    blob.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-blob-container"></a>Ta bort en blob-behållare
Hämta slutligen en blob för att ta bort en blob-behållare, referens för behållaren och anropet **deleteIfExists**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container.
    container.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Blob storage kan du följa dessa länkar att lära dig mer komplexa lagringsuppgifter.

* [Azure Storage SDK för Java][Azure Storage SDK for Java]
* [Referens för Azure Storage Client SDK][referens för Azure Storage Client SDK]
* [Azure Storage REST API][Azure Storage REST API]
* [Azure Storage-teamets blogg][Azure Storage Team Blog]

Mer information finns också [Azure för Java-utvecklare](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[referens för Azure Storage Client SDK]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
