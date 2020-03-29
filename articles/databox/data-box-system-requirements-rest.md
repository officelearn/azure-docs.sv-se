---
title: Microsoft Azure Data Box Blob lagringskrav| Microsoft-dokument
description: Lär dig mer om de versioner som stöds för API:er, SDK:er och klientbibliotek för Azure Data Box Blob-lagring
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436502"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob lagringskrav

I den här artikeln visas versionerna av Azure-API:erna, Azure-klientbibliotek och verktyg som stöds med databoxens Blob-lagring. Data Box Blob storage tillhandahåller blob-hanteringsfunktioner med Azure-konsekvent semantik. Den här artikeln sammanfattar också de kända Azure Data Box Blob-lagringsskillnaderna från Azure Storage-tjänsterna.

Vi rekommenderar att du granskar informationen noggrant innan du ansluter till dataruteblobblagringen och sedan refererar tillbaka till den efter behov.


## <a name="storage-differences"></a>Skillnader i lagring

|     Funktion                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Molnbaserade SMB-filresurser stöds              |    Stöds inte      |
|    Tjänstkryptering för data i vila                  |    256-bitars AES-kryptering                             |    256-bitars AES-kryptering |
|    Storage Account-typ                                 |    Redovisnings- och Azure-bloblagringskonton    |    Endast allmänt ändamål v1|
|    Blobnamn                                            |    1 024 tecken (2 048 byte)                     |    880 tecken (1 760 byte)|
|    Block blob maximal storlek                              |    4,75 TB (100 MB X 50 000 block)                   |    4,75 TB (100 MB x 50 000 block) för Azure Data Box v 1.8 och framåt.|
|    Sidblob maximal storlek                               |    8 TB                                               |    1 TB                   |
|    Sidstorlek på sidrubrik                                  |    512 byte                                          |    4 kB                   |

## <a name="supported-api-versions"></a>API-versioner som stöds

Följande versioner av Azure Storage-tjänst API:er stöds med Data Box Blob-lagring:

Azure Data Box 1.8 och framåt

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Azure-klientbibliotek som stöds

För Data Box Blob-lagring finns det specifika klientbibliotek och specifika krav på slutpunktssuffix. Slutpunkterna för datarump-bloblagring har inte fullständig paritet med den senaste versionen av AZURE Blob Storage REST API, se [de versioner som stöds för Azure Data Box 1.8 och framåt](#supported-api-versions). För lagringsklientbiblioteken måste du vara medveten om den version som är kompatibel med REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 och framåt

| Klientbibliotek     |Version av Data Box Blob-lagring     | Länk   |     Specifikation för slutpunkt      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget paket:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-versionen:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config-fil                 |
|    Java                |    7.0.0                                           |    Maven-paketet:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-versionen:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Inställningar för anslutningssträng         |
|    Node.js             |    2.8.3                                           |    NPM länk: https://www.npmjs.com/package/azure-storage (Kör: `npm install azure-storage@2.7.0`)   <br>GitHub-versionen:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklaration för tjänstinstans    |
|    C++                 |    5.2.0                                           |    Nuget paket:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-versionen:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Inställningar för anslutningssträng         |
|    PHP                 |    1.2.0                                           |    GitHub-versionen:<br>Gemensamma:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installera via Composer (Mer information finns i informationen nedan.)                                                                                                             |    Inställningar för anslutningssträng         |
|    Python              |    1.1.0                                           |    GitHub-versionen:<br>Gemensamma:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklaration för tjänstinstans    |
|    Ruby                |    1.0.1                                           |    RubyGems-paketet:<br>Gemensamma:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-versionen:<br>Gemensamma:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Inställningar för anslutningssträng         |



### <a name="install-php-client-via-composer---current"></a>Installera PHP-klient via Composer - aktuell

Så här installerar du via Composer: (ta blob som exempel).
1. Skapa en fil med namnet composer.json i projektets rot med följande kod:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Hämta `composer.phar` till projektroten.

3. Kör: php composer.phar installera.

### <a name="endpoint-declaration"></a>Förklaring om slutpunkt

En Azure Data Box Blob-lagringsslutpunkt innehåller två delar: namnet på en region och Data Box-domänen. I SDK för datarutebloblagring är `\<serial no. of the device>.microsoftdatabox.com`standardslutpunkten .  Mer information om blob-tjänstslutpunkten finns i [Anslut via dataruteblobblagring](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

För datarumpblolagring anges slutpunktssuffixet `app.config` i filen:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

För datarutebloblagring anges slutpunktssuffixet i installationen av anslutningssträngen:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

För datarutebloblagring anges slutpunktssuffixet i deklarationsinstansen:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

För datarutebloblagring anges slutpunktssuffixet i installationen av anslutningssträngen:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

För datarutebloblagring anges slutpunktssuffixet i installationen av anslutningssträngen:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

För datarutebloblagring anges slutpunktssuffixet i deklarationsinstansen:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

För datarutebloblagring anges slutpunktssuffixet i installationen av anslutningssträngen:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera din Azure-databox](data-box-deploy-ordered.md)
