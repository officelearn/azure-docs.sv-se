---
title: Kom igång med Azure Stack verktyg för lagringsutveckling | Microsoft Docs
description: Vägledning för att komma igång med Azure Stack verktyg för lagringsutveckling
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 02/27/2019
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 45bd47d0a55f447cc6222bb24de8eafcf37e263b
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010658"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Kom igång med Azure Stack verktyg för lagringsutveckling

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Microsoft Azure Stack tillhandahåller en uppsättning lagringstjänster som innehåller blob, tabell och queue storage.

Använd den här artikeln som vägledning för att komma igång med Azure Stack verktyg för lagringsutveckling. Du hittar mer detaljerad information och exempelkod i motsvarande Azure storage-självstudier.

> [!NOTE]  
> Det finns kända skillnader mellan Azure Stack storage och Azure storage, inklusive särskilda krav för varje plattform. Det finns till exempel specifika klientbibliotek och viss slutpunkts-suffixkrav för Azure Stack. Mer information finns i [Azure Stack-lagring: Skillnader och överväganden](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azure-klientbiblioteken

Ta reda på den version som är kompatibel med REST API för storage-klientbibliotek. Du måste även ange Azure Stack-slutpunkten i din kod.

### <a name="1811-update-or-newer-versions"></a>1811 update eller senare versioner

| Klientbibliotek | Azure Stack-version som stöds | Länk | Slutpunkt-specifikation |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Nuget-paketet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0 | filen App.config |
| Java | 7.0.0 | Maven-paketet:<br>https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0 | Anslutningsinställningar för sträng |
| Node.js | 2.8.3 | NPM-länk:<br>https://www.npmjs.com/package/azure-storage<br>(Kör: `npm install azure-storage@2.8.3`)<br> <br>Github-version:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3 | Instans tjänstedeklaration |
| C++ | 5.2.0 | Nuget-paketet:<br>https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0 | Anslutningsinställningar för sträng |
| PHP | 1.2.0 | GitHub-version:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common<br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue<br>Tabell: https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table<br> <br>Installera via Composer (Mer information, [se information nedan](#install-php-client-via-composer---current).) | Anslutningsinställningar för sträng |
| Python | 1.1.0 | GitHub-version:<br>Vanliga:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue | Instans tjänstedeklaration |
| Ruby | 1.0.1 | RubyGems package:<br>Vanliga:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Kö: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabell: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub-version:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Kö: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabell: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Anslutningsinställningar för sträng |

#### <a name="install-php-client-via-composer---current"></a>Installera PHP-klienten via Composer - aktuella

Installera via Composer: (ta bloben som exempel).

1. Skapa en fil med namnet **composer.json** i roten av projektet med följande kod:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Ladda ned [composer.phar](http://getcomposer.org/composer.phar) till rot-projekt.
3. Run: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Tidigare versioner (1802 1809 uppdatering)

| Klientbibliotek | Azure Stack-version som stöds | Länk | Slutpunkt-specifikation |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget-paketet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | filen App.config |
| Java | 6.1.0 | Maven-paketet:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Anslutningsinställningar för sträng |
| Node.js | 2.7.0 | NPM-länk:<br>https://www.npmjs.com/package/azure-storage<br>(Kör: `npm install azure-storage@2.7.0`)<br> <br>Github-version:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Instans tjänstedeklaration |
| C++ | 3.1.0 | Nuget-paketet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub-version:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Anslutningsinställningar för sträng |
| PHP | 1.0.0 | GitHub-version:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabell: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Installera via Composer (se detaljer nedan).) | Anslutningsinställningar för sträng |
| Python | 1.0.0 | GitHub-version:<br>Vanliga:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Instans tjänstedeklaration |
| Ruby | 1.0.1 | RubyGems package:<br>Vanliga:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Kö: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabell: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub-version:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Kö: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabell: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Anslutningsinställningar för sträng |

#### <a name="install-php-client-via-composer---previous"></a>Installera PHP-klienten via Composer - tidigare

Installera via Composer: (gör blob som exempel).

1. Skapa en fil med namnet **composer.json** i roten av projektet med följande kod:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Ladda ned [composer.phar](http://getcomposer.org/composer.phar) till rot-projekt.
3. Run: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Slutpunktsdeklarationen

En slutpunkt i Azure Stack består av två delar: namnet på en region och Azure Stack-domänen.
I Azure Stack Development Kit är standardslutpunkten **local.azurestack.external**.
Kontakta molnadministratören och om du är osäker på om din slutpunkt.

## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

Slutpunktens suffix anges i filen app.config för Azure Stack:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Slutpunktens suffix anges i inställningarna för anslutningssträngen för Azure Stack:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Slutpunktens suffix anges i deklarationen-instans för Azure Stack:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Slutpunktens suffix anges i inställningarna för anslutningssträngen för Azure Stack:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Slutpunktens suffix anges i inställningarna för anslutningssträngen för Azure Stack:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Slutpunktens suffix anges i deklarationen-instans för Azure Stack:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Slutpunktens suffix anges i inställningarna för anslutningssträngen för Azure Stack:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Följande självstudier i Azure Blob storage är tillämpliga på Azure Stack. Observera viss slutpunkts-suffix krav för Azure Stack som beskrivs i föregående [exempel](#examples) avsnittet.

* [Komma igång med Azure Blob Storage med hjälp av .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Använda Blob Storage från Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Använda Blob Storage från Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Använda Blob storage från C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Blob Storage från PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Använda Azure Blob storage från Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Använda Blob Storage från Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Följande självstudier för Azure Queue storage är tillämpliga på Azure Stack. Observera viss slutpunkts-suffix krav för Azure Stack som beskrivs i föregående [exempel](#examples) avsnittet.

* [Komma igång med Azure Queue Storage med hjälp av .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Använda Queue Storage från Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Använda Queue Storage från Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Använda Queue storage från C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Använda Queue Storage från PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Använda Queue Storage från Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Använda Queue Storage från Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Table Storage

Följande självstudier för Azure Table storage är tillämpliga på Azure Stack. Observera viss slutpunkts-suffix krav för Azure Stack som beskrivs i föregående [exempel](#examples) avsnittet.

* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Använda Table Storage från Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Använda Azure Table Storage från Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Använda Table storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Använda Table Storage från PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Hur du använder tabellagring i Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Använda Table Storage från Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Microsoft Azure storage](../../storage/common/storage-introduction.md)
