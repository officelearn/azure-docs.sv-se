---
title: "Kom igång med Azure-stacken Storage utvecklingsverktyg"
description: "Vägledning för att komma igång med Azure-stacken Storage utvecklingsverktyg"
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 02/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 81c62fc569e9f758d08bfca0bdfc5bcc9ed5860f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Kom igång med Azure-stacken Storage utvecklingsverktyg

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Stacken för Microsoft Azure tillhandahåller en uppsättning lagringstjänster, inklusive Azure Blob-, tabell- och Queue storage.

Den här artikeln innehåller snabb råd om hur du börja använda Azure Stack Storage utvecklingsverktyg. Du hittar mer detaljerad information och exempelkod i motsvarande Azure Storage-självstudierna.

Det finns kända skillnader mellan Azure Storage och Azure-stacken lagring, inklusive vissa krav för varje plattform. Det finns till exempel specifika klientbibliotek och viss slutpunkts-suffixkrav för Azure-stacken. Mer information finns i [Azure Stack Storage: skillnader och överväganden](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azures klientbibliotek

De REST API-versionerna som stöds för Azure Storage för stacken är 2017-04-17, 2016-05-31, 11-12-2015, 2015-07-08, 2015-04-05 för 1802 uppdateringen eller senare versioner och 2015-04-05 för tidigare versioner. Stacken för Azure-slutpunkter har inte fullständig paritet med den senaste versionen av Azure Storage REST API. Du måste vara medveten om den version som är kompatibel med REST API för storage-klientbibliotek.

### <a name="1802-update-or-newer-versions"></a>1802 uppdateringen eller senare versioner

| Klientbibliotek | Azure-stacken en version som stöds | Länk | Specifikation för slutpunkten |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget-paketet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | filen App.config |
| Java | 6.1.0 | Maven-paket:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Anslutningsinställningar för sträng |
| Node.js | 2.7.0 | NPM länk:<br>https://www.npmjs.com/package/azure-storage<br>(Kör: `npm install azure-storage@2.7.0`)<br> <br>Github-versionen:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Instansen tjänstedeklaration |
| C++ | 3.1.0 | Nuget-paketet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>GitHub-versionen:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Anslutningsinställningar för sträng |
| PHP | 1.0.0 | GitHub-versionen:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Table: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Installera via Composer (Mer information, [finns i detalj nedan](#install-php-client-via-composer---current).) | Anslutningsinställningar för sträng |
| Python | 1.0.0 | GitHub-versionen:<br>Vanliga:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Kö:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Instansen tjänstedeklaration |
| Ruby | 1.0.1 | RubyGems paket:<br>Vanliga:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>GitHub-versionen:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Queue: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Table: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Anslutningsinställningar för sträng |

#### <a name="install-php-client-via-composer---current"></a>Installera PHP-klienten via Composer - aktuella

Så här installerar du via Composer: (vidta blob som exempel).

1. Skapa en fil med namnet **composer.json** i roten av projektet med följande kod:
  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```
2. Hämta [composer.phar](http://getcomposer.org/composer.phar) rot-projekt.
3. Kör: `php composer.phar install`.

### <a name="previous-versions"></a>Tidigare versioner

|Klientbibliotek|Azure-stacken en version som stöds|Länk|Specifikation för slutpunkten|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget-paketet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|filen App.config|
|Java|4.1.0|Maven-paket:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub-versionen:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Anslutningsinställningar för sträng|
|Node.js     |1.1.0|NPM länk:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(run: `npm install azure-storage@1.1.0)`<br><br>Github-versionen:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Instansen tjänstedeklaration||C++|2.4.0|Nuget-paketet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Anslutningsinställningar för sträng|
|C++|2.4.0|Nuget-paketet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Anslutningsinställningar för sträng|
|PHP|0.15.0|GitHub-versionen:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Installera via Composer (Mer information finns nedan)|Anslutningsinställningar för sträng|
|Python     |0.30.0|PIP-paket:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Kör: `pip install -v azure-storage==0.30.0)`<br><br>GitHub-versionen:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Instansen tjänstedeklaration|
|Ruby|0.12.1<br>Förhandsversion|RubyGems paket:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub-versionen:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Anslutningsinställningar för sträng|

#### <a name="install-php-client-via-composer---previous"></a>Installera PHP-klienten via Composer - tidigare

Installera via Composer:

1. Skapa en fil med namnet **composer.json** i roten av projektet med följande kod:
  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```
2. Hämta [composer.phar](http://getcomposer.org/composer.phar) i projektroten.
3. Kör: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Slutpunktsdeklarationen

En slutpunkt för Azure-stacken innehåller två delar: namnet på en region och Azure Stack-domänen.
I Azure-stacken Development Kit är standardslutpunkten **local.azurestack.external**.
Kontakta molnadministratören och om du inte vet om slutpunkten.

## <a name="examples"></a>Exempel


### <a name="net"></a>.NET

Suffix för slutpunkten har angetts i filen app.config för Azure Stack:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Suffixet slutpunkten anges i inställningarna för anslutningssträngen för Azure Stack:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Suffixet slutpunkten anges i deklarationen-instans för Azure Stack:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Suffixet slutpunkten anges i inställningarna för anslutningssträngen för Azure Stack:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Suffixet slutpunkten anges i inställningarna för anslutningssträngen för Azure Stack:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Suffixet slutpunkten anges i deklarationen-instans för Azure Stack:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Suffixet slutpunkten anges i inställningarna för anslutningssträngen för Azure Stack:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Följande kurser i Azure Blob storage är tillämpliga på Azure-stacken. Observera viss slutpunkts-suffix krav för Azure-stacken som beskrivs i föregående [exempel](#examples) avsnitt.

* [Komma igång med Azure Blob Storage med hjälp av .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Använda Blob Storage från Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Använda Blob Storage från Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Hur du använder Blob storage från C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Blob Storage från PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Använda Azure Blob storage från Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Använda Blob Storage från Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Följande kurser i Azure Queue storage är tillämpliga på Azure-stacken. Observera viss slutpunkts-suffix krav för Azure-stacken som beskrivs i föregående [exempel](#examples) avsnitt.

* [Komma igång med Azure Queue Storage med hjälp av .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Använda Queue Storage från Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Använda Queue Storage från Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Använda Queue storage från C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Använda Queue Storage från PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Använda Queue Storage från Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Använda Queue Storage från Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Table Storage

Följande kurser i Azure Table storage är tillämpliga på Azure-stacken. Observera viss slutpunkts-suffix krav för Azure-stacken som beskrivs i föregående [exempel](#examples) avsnitt.

* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Använda Table Storage från Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Använda Azure Table Storage från Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Använda Table storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Använda Table Storage från PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Använda Table storage i Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Använda Table Storage från Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)