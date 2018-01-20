---
title: "Använda Blob storage (objektlagring) från Ruby | Microsoft Docs"
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: 3a21d87cee714dbc3aab6d4106544e45d91a3193
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>Använda Blob Storage från Ruby
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Översikt
Azure Blob Storage är en tjänst som lagrar ostrukturerade data i molnet som objekt/blobbar. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.

Den här guiden visar hur du utför vanliga scenarier med Blob storage. Exemplen är skrivna med hjälp av Ruby-API. Scenarier som tas upp inkluderar **överför, lista, hämtar** och **bort** blobbar.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Skapa ett Ruby program. Instruktioner finns i [Ruby på spår webbprogram på en Azure VM](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)


## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program åtkomst till lagring
Om du vill använda Azure Storage, måste du hämtar och använder Ruby azure-paketet, som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Använda RubyGems för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Skriv ”symbolen installera azure storage blob” i kommandofönstret att installera symbolen och beroenden.

### <a name="import-the-package"></a>Importera paketet
Med hjälp av valfri textredigerare, lägger du till följande upp i filen Ruby där du tänker använda lagring:

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>Skapa en Azure Storage-anslutning
Azure-modulen läses miljövariablerna **AZURE\_lagring\_konto** och **AZURE\_lagring\_ACCESS_KEY** information som krävs för att ansluta till Azure storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontot information med **Azure::Blob::BlobService:: skapa** med följande kod:

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

Du kan hämta dessa värden från en klassiska eller Resource Manager storage-konto i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till storage-konto som du vill använda.
3. I bladet inställningar till höger klickar du på **åtkomstnycklar**.
4. I åtkomst bladet nycklar som visas, visas den åtkomstnyckel 1 och åtkomstnyckel 2. Du kan använda någon av dessa.
5. Klicka på Kopiera-ikonen för att kopiera nyckeln till Urklipp.

## <a name="create-a-container"></a>Skapa en behållare
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Den **Azure::Storage::Blob::BlobService** objekt kan du arbeta med behållare och blobbar. Du kan skapa en behållare med den **skapa\_container()** metod.

Följande exempel skapar en behållare eller skriver ut felet om sådana finns.

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

Om du vill göra filerna i behållaren offentliga kan du ange behållarens behörigheter.

Du kan bara ändra det <strong>skapa\_container()</strong> anrop för att skicka den **: offentliga\_åtkomst\_nivå** alternativ:

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

Giltiga värden för den **: offentliga\_åtkomst\_nivå** alternativ är:

* **BLOB:** anger offentlig läsbehörighet för blobbar. BLOB-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgänglig. Klienter kan inte räkna upp blobbar i behållaren via anonym begäran.
* **behållare:** anger fullständig offentlig läsbehörighet för behållaren och blob-data. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

Du kan också ändra offentliga åtkomstnivån för en behållare med hjälp av **ange\_behållare\_acl()** metod för att ange nivån offentlig åtkomst.

Följande exempel ändras offentliga åtkomstnivå till **behållare**:

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Om du vill överföra innehåll till en blobb, använder den **skapa\_block\_blob()** metoden för att skapa blob, använder en fil eller en sträng som innehåll i blob.

Följande kod överför filen **test.png** som en ny blob med namnet ”-avbildningsbloben” i behållaren.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
Om du vill visa behållarna som använder **list_containers()** metod.
Om du vill visa blobbar i en behållare använder **lista\_blobs()** metod. För att visa en lista med alla blobbar i en behållare, måste du följa en fortsättningstoken som returnerades av tjänsten och fortsätta att köra list_blobs med denna token. Finns det [lista BLOB REST API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) mer information.

Följande kod visar de alla blobbarna i en behållare.

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>Ladda ned blobbar
Om du vill ladda ned blobbar använda av **hämta\_blob()** metod för att hämta innehållet.

I följande kodexempel visas hur du använder **hämta\_blob()** att hämta innehållet i ”avbildningsbloben” och skriver den till en lokal fil.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>Ta bort en Blobb
Slutligen, om du vill ta bort en blobb, använda den **ta bort\_blob()** metod. Följande kodexempel visar hur du tar bort en blob.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>Nästa steg
Mer information om mer komplexa lagringsuppgifter, kan du följa dessa länkar:

* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage-SDK för Ruby](https://github.com/azure/azure-storage-ruby) databasen på GitHub
* [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

