---
title: "Kom igång med Azure-stacken Storage utvecklingsverktyg"
description: "Vägledning för att komma igång med Azure-stacken Storage utvecklingsverktyg"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Kom igång med Azure-stacken Storage utvecklingsverktyg

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*


Stacken för Microsoft Azure tillhandahåller en uppsättning lagringstjänster, inklusive Azure Blob-, tabell- och Queue storage.

Den här artikeln innehåller snabb råd om hur du börja använda Azure Stack Storage utvecklingsverktyg. Du hittar mer detaljerad information och exempelkod i motsvarande Azure Storage-självstudierna.

Det finns kända skillnader mellan Azure Storage och Azure-stacken lagring, inklusive vissa krav för varje plattform. Det finns till exempel specifika klientbibliotek och viss slutpunkts-suffixkrav för Azure-stacken. Mer information finns i [Azure Stack Storage: skillnader och överväganden](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Azures klientbibliotek
Den REST API-versionen som stöds för Azure Storage för stacken är 2015-04-05. Den har inte fullständiga paritet med den senaste versionen av Azure Storage REST API. Så för lagringsklientbiblioteken behöver du känna till den version som är kompatibel med REST API 2015-04-05.


|Klientbibliotek|Azure-stacken en version som stöds|Länk|Specifikation för slutpunkten|
|---------|---------|---------|---------|
|.NET     |6.2.0|Nuget-paketet:<br>[https://www.nuget.org/Packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/Azure-Storage-NET/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|filen App.config|
|Java|4.1.0|Maven-paket:<br>[http://mvnrepository.com/Artifact/com.microsoft.Azure/Azure-Storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>GitHub-versionen:<br> [https://github.com/Azure/Azure-Storage-Java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Anslutningsinställningar för sträng|
|Node.js     |1.1.0|NPM länk:<br>[https://www.npmjs.com/Package/Azure-Storage](https://www.npmjs.com/package/azure-storage)<br>(kör:`npm install azure-storage@1.1.0)`<br><br>Github-versionen:<br>[https://github.com/Azure/Azure-Storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Instansen tjänstedeklaration||C++|2.4.0|Nuget-paketet:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Anslutningsinställningar för sträng|
|C++|2.4.0|Nuget-paketet:<br>[https://www.nuget.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>GitHub-versionen:<br>[https://github.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Anslutningsinställningar för sträng|
|PHP|0.15.0|GitHub-versionen:<br>[https://github.com/Azure/Azure-Storage-PHP/releases/tag/V0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Installera via Composer (Mer information finns nedan)|Anslutningsinställningar för sträng|
|Python     |0.30.0|PIP-paket:<br> [https://pypi.Python.org/pypi/Azure-Storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Kör:`pip install -v azure-storage==0.30.0)`<br><br>GitHub-versionen:<br> [https://github.com/Azure/Azure-Storage-Python/releases/tag/V0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Instansen tjänstedeklaration|
|Ruby|0.12.1<br>Förhandsversion|RubyGems paket:<br> [https://rubygems.org/GEMS/Azure-Storage/versions/0.12.1.Preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>GitHub-versionen:<br> [https://github.com/Azure/Azure-Storage-Ruby/releases/tag/V0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Anslutningsinställningar för sträng|

> [!NOTE]
> PHP-information<br><br>
>Installera via Composer:
>1. Skapa en fil med namnet `composer.json` i roten av projektet med följande kod:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Hämta [composer.phar](http://getcomposer.org/composer.phar) i projektroten.
>3. Kör: `php composer.phar install`.
>


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
* [Hur du använder Blob storage från Node.js...] /.. / storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
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
* [Hur du använder Azure Table storage från Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Använda Table storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Använda Table Storage från PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Använda Table storage i Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Använda Table Storage från Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)