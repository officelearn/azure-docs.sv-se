---
title: "Hur du använder blob storage (objektlagring) från C++ | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: .net
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 53844120-1c48-4e2f-8f77-5359ed0147a4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: daf480b7be78dc001712010eac6386d4744c3c1d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-use-blob-storage-from-c"></a>Hur du använder Blob Storage från C++
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här guiden visar hur du utför vanliga scenarier som använder tjänsten Azure Blob storage. Exemplen är skrivna i C++ och Använd den [Azure Storage-klientbibliotek för C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenarier som tas upp inkluderar **överför**, **lista**, **hämtar**, och **bort** blobbar.  

> [!NOTE]
> Den här handboken riktar sig mot Azure Storage-klientbibliotek för C++ version 1.0.0 och senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängliga via [NuGet](http://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder lagringsfunktioner som kan köras i ett C++-program.  

Om du vill göra det, behöver du installera Azure Storage-klientbibliotek för C++ och skapa ett Azure storage-konto i din Azure-prenumeration.   

Om du vill installera Azure Storage-klientbibliotek för C++ kan du använda följande metoder:

* **Linux:** Följ instruktionerna den [Azure Storage-klientbibliotek för C++ viktigt](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sidan.  
* **Windows:** i Visual Studio klickar du på **Verktyg > NuGet Package Manager > Package Manager-konsolen**. Skriv följande kommando i den [NuGet Package Manager-konsolen](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på **RETUR**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurera ditt program för att få åtkomst till Blob Storage
Lägga till följande uttryck överst i filen C++ där du vill använda Azure storage API: er för att få åtkomst till blobbar:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
```

## <a name="setup-an-azure-storage-connection-string"></a>Ställ in en anslutningssträng för Azure storage
Ett Azure storage-klienten använder en anslutningssträng för lagring för att lagra slutpunkter och autentiseringsuppgifter för åtkomst till data management services. När den körs i ett klientprogram, du måste ange anslutningssträngen för lagring i följande format, med hjälp av namnet på ditt lagringskonto och åtkomstnyckeln lagring för lagringskontot som anges i den [Azure Portal](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. Information om lagringskonton och snabbtangenterna finns [om Azure Lagringskonton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Det här exemplet visar hur du kan deklarera statiska fält att lagra anslutningssträngen:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program i din lokala Windows-dator, du kan använda Microsoft Azure [lagringsemulatorn](../storage-use-emulator.md) som installeras med den [Azure SDK](https://azure.microsoft.com/downloads/). Storage-emulatorn är ett verktyg som simulerar Blob, köer och tabellen tjänster som är tillgängliga i Azure på utvecklingsdatorn lokala. I följande exempel visas hur du kan deklarera statiska fält för anslutningssträngen till din lokala storage-emulatorn:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure storage-emulatorn, Välj den **starta** knappen eller tryck på den **Windows** nyckel. Börja skriva **Azure Storage-emulatorn**, och välj **Microsoft Azure Storage-emulatorn** från listan med program.  

Följande exempel förutsätter att du har använt ett av dessa två sätt för att hämta anslutningssträngen för lagring.  

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda den **cloud_storage_account** klass för att representera kontoinformationen för lagring. Du kan använda för att hämta information om ditt lagringskonto från anslutningssträngen för lagring av **parsa** metod.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Sedan hämta en referens till en **cloud_blob_client** klassen som du kan hämta objekt som representerar behållare och blobbar som lagras i Blob Storage-tjänst. Följande kod skapar en **cloud_blob_client** objekt med kontot lagringsobjektet vi hämta ovan:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Så här: skapa en behållare
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Det här exemplet visas hur du skapar en behållare om den inte redan finns:  

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

Den nya behållaren är privat som standard, och du måste ange din lagringsåtkomstnyckel för att ladda ned blobbar från den här behållaren. Om du vill göra filerna (BLOB) i behållaren tillgängliga för alla kan du ange att behållaren ska vara offentlig med hjälp av följande kod:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Alla på Internet kan se blobbar i en offentlig behållare, men du kan ändra eller ta bort dem bara om du har rätt åtkomstnyckel.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Så här: ladda upp en blobb till en behållare
Azure Blob Storage stöder blockblobbar och sidblobbar. I de flesta fall är blockblobbar den rekommenderade typen.  

Om du vill ladda upp en fil till en blockblobb hämtar du en referens för behållaren och använder den för att hämta en referens för blockblobben. När du har en blobbreferens kan du ladda upp en dataström till den genom att anropa den **upload_from_stream** metod. Med den här åtgärden skapas blobben om den inte fanns tidigare, eller skrivs över om den redan fanns. Följande exempel visar hur du laddar upp en blobb till en behållare och förutsätter att behållaren redan hade skapats.  

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

Du kan också använda den **upload_from_file** metod för att överföra en fil till en blockblobb.

## <a name="how-to-list-the-blobs-in-a-container"></a>Så här: Visa blobbar i en behållare
Om du vill visa blobbar i en behållare börjar du med att hämta en referens för behållaren. Du kan sedan använda behållarens **list_blobs** metod för att hämta blobbarna och/eller katalogerna i den. Att komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **list_blob_item**, måste du anropa den **list_blob_item.as_blob** metod för att hämta en **cloud_blob** objektet, eller **list_blob.as_directory** metod för att hämta ett cloud_blob_directory-objekt. Följande kod visar hur du hämtar och returnerar URI: N för varje objekt i den **Mina exempel behållaren** behållare:

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

Mer information om hur du visar operations finns [lista Azure Storage-resurser i C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Så här: ladda ned blobbar
Om du vill ladda ned blobbar först hämta en blobbreferens och anropar den **download_to_stream** metod. I följande exempel används den **download_to_stream** metod för att överföra blobbinnehållet till ett stream-objekt som du sedan kan spara till en lokal fil.  

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

Du kan också använda den **download_to_file** metod för att hämta innehållet i en blobb till en fil.
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

## <a name="how-to-delete-blobs"></a>Så här: ta bort blobbar
Om du vill ta bort en blobb först hämta en blobbreferens och anropar den **delete_blob** metod på den.  

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
* [Storage-klientbibliotek för C++-referens](http://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
* [Överföra data med kommandoradsverktyget AzCopy](../storage-use-azcopy.md)

