---
title: Hur du använder objektlagring (Blob) från C++ - Azure | Microsoft Docs
description: Store Ostrukturerade data i molnet med Azure-blobblagringen (objekt).
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: d86b2c71515900405b0e7714d2c36cd8e4cbc7fc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122616"
---
# <a name="how-to-use-blob-storage-from-c"></a>Använda Blob storage från C++

Den här guiden visar hur du utför vanliga scenarier med Azure Blob storage. Exemplen visar hur du laddar upp, lista, hämta och ta bort blobar. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Mer information om Blob storage finns [introduktion till Azure Blob storage](storage-blobs-introduction.md).

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Microsoft rekommenderar att du använder den senaste versionen av Storage-klientbiblioteket för C++, tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder lagringsfunktioner som kan köras i ett C++-program.  

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.   

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

* **Linux:** Följ instruktionerna den [Azure Storage-klientbiblioteket för C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sidan.  
* **Windows:** Klicka på **Verktyg > NuGet Package Manager > Package Manager Console** i Visual Studio. Skriv följande kommando i den [NuGet Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på **RETUR**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurera programmet för att få åtkomst till Blob storage
Lägg till följande ingår satser överst i filen C++ där du vill använda API: er för Azure storage för att få åtkomst till blobar:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurera en anslutningssträng för Azure storage
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klientprogram kan du måste ange anslutningssträng för lagring i följande format, med hjälp av namnet på ditt lagringskonto och åtkomstnyckel för lagring för lagringskontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. Information om storage-konton och åtkomstnycklar finns [om Azure Storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program i din lokala Windows-dator, kan du använda Microsoft Azure [lagringsemulatorn](../storage-use-emulator.md) som installeras med den [Azure SDK](https://azure.microsoft.com/downloads/). Storage-emulatorn är ett verktyg som simulerar Blob, Queue och Table tjänster som är tillgängliga i Azure på din lokala utvecklingsdator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure storage-emulatorn, Välj den **starta** knapp eller genom att trycka på den **Windows** nyckel. Börja skriva **Azure Storage-emulatorn**, och välj **Microsoft Azure Storage-emulatorn** från listan med program.  

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.  

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda den **cloud_storage_account** klass för att representera kontoinformationen för lagring. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Sedan hämta en referens till en **cloud_blob_client** klassen eftersom den låter dig hämta objekt som representerar behållare och blobbar som lagras i Blob storage. Följande kod skapar en **cloud_blob_client** objekt med hjälp av kontot lagringsobjektet vi hämtade ovan:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Anvisningar: Skapa en container
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Det här exemplet visas hur du skapar en container om den inte redan finns:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Ny behållare är privat som standard, och du måste ange din lagringsåtkomstnyckel för att ladda ned blobbar från den här behållaren. Om du vill att filerna (blobbar) i behållaren tillgängliga för alla kan du ange att behållaren ska vara offentlig med följande kod:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Alla på Internet kan se blobbar i en offentlig behållare, men du kan ändra eller ta bort dem bara om du har rätt åtkomstnyckel.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Anvisningar: Ladda upp en blob till en container
Azure Blob storage stöder blockera blobar och sidblobar. I de flesta fall är blockblobbar den rekommenderade typen.  

Om du vill ladda upp en fil till en blockblob hämtar du en referens för containern och använder den för att hämta en referens för blockbloben. När du har en blobbreferens kan du kan ladda upp en dataström till den genom att anropa den **upload_from_stream** metod. Med den här åtgärden skapas blobben om den inte fanns tidigare, eller skrivs över om den redan fanns. Följande exempel visar hur du laddar upp en blob till en container och förutsätter att containern redan hade skapats.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Du kan också använda den **upload_from_file** metod för att överföra en fil till en blockblob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Anvisningar: Visa en lista över blobarna i en container
Om du vill visa blobar i en container börjar du med att hämta en referens för containern. Du kan sedan använda behållarens **list_blobs** metod för att hämta blobbarna och/eller katalogerna i den. Komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **list_blob_item**, måste du anropa den **list_blob_item.as_blob** metod för att hämta en **cloud_blob** objekt, eller **list_blob.as_directory** metod för att hämta ett cloud_blob_directory-objekt. Följande kod visar hur du hämtar och returnerar URI: N för varje objekt i den **Mina exempelbehållaren** behållare:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Mer information om hur du visar åtgärder finns i [lista Azure Storage-resurser i C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Anvisningar: Ladda ned blobbar
Om du vill ladda ned blobbarna först hämta en blobbreferens och anropar sedan den **download_to_stream** metod. I följande exempel används den **download_to_stream** metod för att överföra blobbinnehållet till ett dataströmsobjekt som du sedan kan spara till en lokal fil.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Du kan också använda den **download_to_file** metod för att hämta innehållet i en blob till en fil.
Dessutom kan du kan också använda den **download_text** metod för att hämta innehållet i en blob som en textsträng.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Anvisningar: Ta bort blobbar
Ta bort en blob genom att först hämta en blobbreferens och anropar sedan den **delete_blob** metod på den.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om blob storage kan du följa dessa länkar om du vill veta mer om Azure Storage.  

* [Använda Queue Storage från C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista över Azure Storage-resurser i C++](../storage-c-plus-plus-enumeration.md)
* [Storage-klientbibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
* [Överföra data med kommandoradsverktyget AzCopy](../storage-use-azcopy.md)

