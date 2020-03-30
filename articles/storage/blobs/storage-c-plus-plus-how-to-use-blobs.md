---
title: Så här använder du objektlagring (Blob) från C++ - Azure | Microsoft-dokument
description: Lagra ostrukturerade data i molnet med Azure Blob-lagring (objekt).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941814"
---
# <a name="how-to-use-blob-storage-from-c"></a>Så här använder du Blob-lagring från C++

Den här guiden visar hur du utför vanliga scenarier med Azure Blob-lagring. Exemplen visar hur du laddar upp, listar, hämtar och tar bort blobbar. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Mer information om Blob-lagring finns i [Introduktion till Azure Blob storage](storage-blobs-introduction.md).

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Microsoft rekommenderar att du använder den senaste versionen av Storage Client Library for C++, som är tillgänglig via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder du lagringsfunktioner som kan köras i ett C++-program.  

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.   

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

* **Linux:** Följ instruktionerna i [Azure Storage Client Library for C++ README: Komma igång på Linux-sidan.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Windows:** I Windows använder du [vcpkg](https://github.com/microsoft/vcpkg) som beroendehanterare. Följ [snabbstarten](https://github.com/microsoft/vcpkg#quick-start) för att initiera vcpkg. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du kan hitta en guide för hur du skapar källkoden och exporterar till NuGet i [README-filen.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurera ditt program för åtkomst till Blob-lagring
Lägg till följande inkludera-satser högst upp i C++-filen där du vill använda Azure storage API:er för att komma åt blobbar:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurera en Azure-lagringsanslutningssträng
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klientprogram måste du ange lagringsanslutningssträngen i följande format med namnet på ditt lagringskonto och lagringsåtkomstnyckeln för lagringskontot som anges i [Azure Portal](https://portal.azure.com) för *accountName-* och *AccountKey-värdena.* Information om lagringskonton och åtkomstnycklar finns i [Om Azure Storage Accounts](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program på din lokala Windows-dator kan du använda Microsoft [Azure-lagringsemulatorn](../storage-use-emulator.md) som är installerad med [Azure SDK](https://azure.microsoft.com/downloads/). Lagringsemulatorn är ett verktyg som simulerar Blob-, kö- och tabelltjänsterna som är tillgängliga i Azure på din lokala utvecklingsdator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure-lagringsemulatorn väljer du **Start-knappen** eller trycker på Windows-tangenten. **Windows** Börja skriva **Azure Storage Emulator**och välj **Microsoft Azure Storage Emulator** i listan över program.  

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.  

## <a name="retrieve-your-storage-account"></a>Hämta ditt lagringskonto
Du kan använda **klassen cloud_storage_account** för att representera din lagringskontoinformation. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Hämta sedan en referens till en **cloud_blob_client** klass eftersom du kan hämta objekt som representerar behållare och blobbar som lagras i Blob-lagring. Följande kod skapar ett **cloud_blob_client** objekt med hjälp av lagringskontoobjektet som vi hämtade ovan:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Så här skapar du en behållare
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

Som standard är den nya behållaren privat och du måste ange din lagringsåtkomstnyckel för att hämta blobbar från den här behållaren. Om du vill göra filerna (blobbar) i behållaren tillgängliga för alla kan du ställa in behållaren så att den är offentlig med hjälp av följande kod:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Alla på Internet kan se blobbar i en offentlig behållare, men du kan bara ändra eller ta bort dem om du har rätt åtkomstnyckel.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Så här laddar du upp en blob till en behållare
Azure Blob-lagring stöder blockblobar och sidblobar. I de flesta fall är blockblobbar den rekommenderade typen.  

Om du vill ladda upp en fil till en blockblob hämtar du en referens för containern och använder den för att hämta en referens för blockbloben. När du har en blob-referens kan du ladda upp alla dataströmmer till den genom att anropa **upload_from_stream** metoden. Med den här åtgärden skapas blobben om den inte fanns tidigare, eller skrivs över om den redan fanns. Följande exempel visar hur du laddar upp en blob till en container och förutsätter att containern redan hade skapats.  

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

Du kan också använda **metoden upload_from_file** för att överföra en fil till en blockblob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Så här: Lista blobbar i en behållare
Om du vill visa blobar i en container börjar du med att hämta en referens för containern. Du kan sedan använda behållarens **list_blobs** metod för att hämta blobbar och/eller kataloger i den. Om du vill komma åt den omfattande uppsättningen egenskaper och metoder för en returnerad **list_blob_item**måste du anropa **metoden list_blob_item.as_blob** för att hämta ett **cloud_blob** objekt eller **metoden list_blob.as_directory** för att hämta ett cloud_blob_directory objekt. Följande kod visar hur du hämtar och matar ut URI för varje objekt i behållaren **för min exempelbehållare:**

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

Mer information om hur du listar åtgärder finns [i Lista Azure Storage Resources i C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Så här: Ladda ner blobbar
Om du vill hämta blobbar hämtar du **download_to_stream** först en blob-referens och anropar sedan download_to_stream-metoden. I följande exempel används **download_to_stream** metoden för att överföra blob-innehållet till ett flödesobjekt som du sedan kan spara till en lokal fil.  

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

Du kan också använda **download_to_file** metoden för att hämta innehållet i en blob till en fil.
Dessutom kan du också använda **download_text** metoden för att hämta innehållet i en blob som en textsträng.  

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

## <a name="how-to-delete-blobs"></a>Så här tar du bort blobbar
Om du vill ta bort en blob får du först en blob-referens och anropar sedan **delete_blob** metoden på den.  

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
Nu när du har lärt dig grunderna i blob-lagring följer du dessa länkar för att lära dig mer om Azure Storage.  

* [Så använder du Queue Storage från C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Så här använder du Tabelllagring från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista Azure Storage-resurser i C++](../storage-c-plus-plus-enumeration.md)
* [Lagringsklientbibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
* [Överföra data med kommandoradsverktyget AzCopy](../storage-use-azcopy.md)

