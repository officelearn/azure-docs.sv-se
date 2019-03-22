---
title: Krav för Microsoft Azure Data Box Blob | Microsoft Docs
description: 'Läs mer om versionerna som stöds för API: er och SDK: er klientbibliotek för Azure Data Box Blob storage'
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 5770cd9a4955013100b7e58698eed77da10c0583
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012255"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Krav för Azure Data Box-Blob-lagring

Den här artikeln innehåller versionerna av Azure API: er, SDK: er och verktyg som stöds med blobblagring för Data Box. Data Box Blob storage tillhandahåller blob hanteringsfunktioner med Azure-konsekventa semantik. Den här artikeln sammanfattas också kända Azure Data Box Blob storage skillnaderna från Azure Storage-tjänsterna.

Vi rekommenderar att du läser informationen noggrant innan du ansluter till Data Box-Blob-lagringen och referera tillbaka till det efter behov.


## <a name="storage-differences"></a>Storage-skillnader

|     Funktion                                             |     Azure Storage                                     |     Data Box-Blob-lagring |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure-fillagring                                   |    Molnbaserade SMB-filresurser som stöds              |    Stöds inte      |
|    Kryptering av lagringstjänst för vilande data                  |    256-bitars AES-kryptering                             |    256-bitars AES-kryptering |
|    Storage Account-typ                                 |    Allmänna och Azure blob storage-konton    |    General-Purpose v1|
|    Blobnamn                                            |    1 024 tecken (2 048 byte)                     |    880 tecken (1,760 byte)|
|    Maximal storlek för block blob                              |    4,75 TB (100 MB X 50 000 block)                   |    4,75 TB (100 MB x 50 000 block) för Azure Data Box v 1.8 och senare.|
|    Maximal sidstorlek för blob                               |    8 TB                                               |    1 TB                   |
|    Sidan blob sidstorlek                                  |    512 byte                                          |    4 KB                   |

## <a name="supported-api-versions"></a>API-versioner som stöds

Följande versioner av Azure Storage-tjänstens API: er stöds med blobblagring för Data Box:

Offentliga förhandsversionen (Azure Data Box 1.8 och senare)

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>SDK-versioner som stöds

|     Klientbibliotek     |     Data Box Blob storage-version som stöds     |     Länk             |     Slutpunkt-specifikation         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    Från 6.2.0 till 8.7.0.                         |    Nuget-paketet:   https://www.nuget.org/packages/WindowsAzure.Storage/ <br>GitHub-version:   https://github.com/Azure/azure-storage-net/releases                                                                      |    filen App.config                 |
|    Java                |    Från 4.1.0 till 6.1.0                          |    Maven-paketet:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>GitHub-version:   https://github.com/Azure/azure-storage-java/releases                                                      |    Anslutningsinställningar för sträng         |
|    Node.js             |    Från 1.1.0 till 2.7.0                          |    NPM-länk:   https://www.npmjs.com/package/azure-storage   (Till exempel: kör ”npm-installationsprogrammet azure-storage@2.7.0”)   <br>GitHub-version:   https://github.com/Azure/azure-storage-node/releases                            |    Instans tjänstedeklaration    |
|    C++                 |    Från 2.4.0 till 3.1.0                          |    Nuget-paketet:   https://www.nuget.org/packages/wastorage.v140/   <br>GitHub-version:   https://github.com/Azure/azure-storage-cpp/releases                                                                            |    Anslutningsinställningar för sträng         |
|    PHP                 |    Från 0.15.0 till 1.0.0                         |    GitHub-version:   https://github.com/Azure/azure-storage-php/releases   <br>Installera via Composer (se detaljer nedan)                                                                                                   |    Anslutningsinställningar för sträng         |
|    Python              |    Från 0.30.0 till 1.0.0                         |    GitHub-version:   https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    Instans tjänstedeklaration    |
|    Ruby                |    Från 0.12.1 till 1.0.1                         |    RubyGems package:<br>Vanliga:   https://rubygems.org/gems/azure-storage-common/   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/      <br>GitHub-version:   https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>Azure-klientbiblioteken som stöds

För Data Box-Blob-lagring finns specifika klientbibliotek och viss slutpunkts-suffixkrav. Data Box Blob storage-slutpunkter har inte fullständig paritet med den senaste versionen av Azure Blob Storage REST API, finns i den [versioner som stöds för Azure Data Box 1.8 och senare](#supported-api-versions). Du behöver känna till den version som är kompatibel med REST API för storage-klientbibliotek.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 och senare

| Klientbibliotek     |Data Box Blob storage-version som stöds     | Länk   |     Slutpunkt-specifikation      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    Nuget-paketet:   https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>GitHub-version:   https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    filen App.config                 |
|    Java                |    6.1.0                                           |    Maven-paketet:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-version:   https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    Anslutningsinställningar för sträng         |
|    Node.js             |    2.7.0                                           |    NPM-länk:   https://www.npmjs.com/package/azure-storage   (Kör: Installera npm azure-storage@2.7.0)   <br>GitHub-version:   https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    Instans tjänstedeklaration    |
|    C++                 |    3.1.0                                           |    Nuget-paketet:   https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>GitHub-version:   https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    Anslutningsinställningar för sträng         |
|    PHP                 |    1.0.0                                           |    GitHub-version:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>Installera via Composer (om du vill veta mer, se information nedan.)                                                                                                             |    Anslutningsinställningar för sträng         |
|    Python              |    1.0.0                                           |    GitHub-version:<br>Vanliga:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOB:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    Instans tjänstedeklaration    |
|    Ruby                |    1.0.1                                           |    RubyGems package:<br>Vanliga:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-version:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Anslutningsinställningar för sträng         |



### <a name="install-php-client-via-composer---current"></a>Installera PHP-klienten via Composer - aktuella

Installera via Composer: (gör blob som exempel).
Skapa en fil med namnet composer.json i roten av projektet med följande kod:

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

Ladda ned `composer.phar` till rot-projekt.

Kör: Installera php composer.phar.

### <a name="endpoint-declaration"></a>Slutpunktsdeklarationen

En Azure Data Box Blob storage-slutpunkt innehåller två delar: namnet på en region och Data Box-domänen. I Data Box-Blob-storage SDK är standardslutpunkten \<serienummer. enhetens >. microsoftdatabox.com.  Mer information om blob service-slutpunkt, går du till [Anslut via blobblagring för Data Box](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

För Data Box-Blob-lagring, slutpunktens suffix har angetts i den `app.config` fil:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Slutpunktens suffix anges i installationen av anslutningssträngen för Data Box Blob-lagring:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Slutpunktens suffix anges i deklarationen-instans för Data Box Blob-lagring:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Slutpunktens suffix anges i installationen av anslutningssträngen för Data Box Blob-lagring:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Slutpunktens suffix anges i installationen av anslutningssträngen för Data Box Blob-lagring:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Slutpunktens suffix anges i deklarationen-instans för Data Box Blob-lagring:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Slutpunktens suffix anges i installationen av anslutningssträngen för Data Box Blob-lagring:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
