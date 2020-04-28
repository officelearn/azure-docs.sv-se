---
title: Microsoft Azure Data Box-enhet Blob Storage-krav | Microsoft Docs
description: 'Lär dig mer om de versioner som stöds för API: er, SDK: er och klient bibliotek för Azure Data Box Blob Storage'
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "61436502"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob Storage-krav

Den här artikeln innehåller en lista över de Azure-API: er, Azures klient bibliotek och verktyg som stöds med Data Box-enhet Blob Storage. Data Box-enhet Blob Storage tillhandahåller BLOB Management-funktioner med Azure-konsekvent semantik. Den här artikeln sammanfattar också kända Azure Data Boxs-Blob Storage-skillnader från Azure Storage tjänsterna.

Vi rekommenderar att du läser informationen noggrant innan du ansluter till Data Box-enhet Blob Storage och sedan går tillbaka till den vid behov.


## <a name="storage-differences"></a>Lagrings skillnader

|     Funktion                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Molnbaserade SMB-filresurser stöds              |    Stöds inte      |
|    Tjänst kryptering för vilande data                  |    256-bitars AES-kryptering                             |    256-bitars AES-kryptering |
|    Storage Account-typ                                 |    Generell användnings-och Azure Blob Storage-konton    |    Allmän användning endast v1|
|    Blobnamn                                            |    1 024 tecken (2 048 byte)                     |    880 tecken (1 760 byte)|
|    Maximal storlek för Block Blob                              |    4,75 TB (100 MB X 50 000 block)                   |    4,75 TB (100 MB x 50 000 block) för Azure Data Box v 1,8 och senare.|
|    Maximal storlek för Page BLOB                               |    8 TB                                               |    1 TB                   |
|    Sid storlek för sid-BLOB                                  |    512 byte                                          |    4 kB                   |

## <a name="supported-api-versions"></a>API-versioner som stöds

Följande versioner av Azure Storage Service-API: er stöds med Data Box-enhet Blob Storage:

Azure Data Box 1,8 och senare

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Azure-klientcertifikat som stöds

För Data Box-enhet Blob Storage finns det särskilda klient bibliotek och särskilda krav för slut punkts suffix. Data Box-enhet Blob Storage-slutpunkterna har inte fullständig paritet med den senaste versionen av Azure Blob Storage REST API, se de [versioner som stöds för Azure Data Box 1,8 och senare](#supported-api-versions). För lagrings klient biblioteken måste du vara medveten om vilken version som är kompatibel med REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1,8 och senare

| Klientbibliotek     |Version som stöds av Data Box-enhet Blob Storage     | Länk   |     Slut punkts specifikation      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    NuGet-paket:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub-utgåva:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app. config-fil                 |
|    Java                |    7.0.0                                           |    Maven-paket:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub-utgåva:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Konfiguration av anslutnings sträng         |
|    Node.js             |    2.8.3                                           |    NPM-länk https://www.npmjs.com/package/azure-storage : (kör `npm install azure-storage@2.7.0`:)   <br>GitHub-utgåva:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Instans deklaration för tjänst instans    |
|    C++                 |    5.2.0                                           |    NuGet-paket:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub-utgåva:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Konfiguration av anslutnings sträng         |
|    PHP                 |    1.2.0                                           |    GitHub-utgåva:<br>Logaritmhttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>BLOBhttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installera via Composer (mer information finns i informationen nedan.)                                                                                                             |    Konfiguration av anslutnings sträng         |
|    Python              |    1.1.0                                           |    GitHub-utgåva:<br>Logaritmhttps://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>BLOBhttps://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Instans deklaration för tjänst instans    |
|    Ruby                |    1.0.1                                           |    RubyGems-paket:<br>Logaritmhttps://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>BLOBhttps://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub-utgåva:<br>Logaritmhttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>BLOBhttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Konfiguration av anslutnings sträng         |



### <a name="install-php-client-via-composer---current"></a>Installera PHP-klient via Composer-Current

Installera via Composer: (ta blob som exempel).
1. Skapa en fil med namnet Compose. json i roten i projektet med följande kod:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Ladda `composer.phar` ned till projekt roten.

3. Kör: php Composer. Phar-installation.

### <a name="endpoint-declaration"></a>Slut punkts deklaration

En Azure Data Box Blob Storage-slutpunkt innehåller två delar: namnet på en region och domänen Data Box-enhet. I Data Box-enhet Blob Storage SDK är `\<serial no. of the device>.microsoftdatabox.com`standard slut punkten.  Mer information om BLOB service-slutpunkt finns i [ansluta via data Box-enhet Blob Storage](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

För Data Box-enhet Blob Storage anges slut punktens suffix i `app.config` filen:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

För Data Box-enhet Blob Storage anges slut punktens suffix i deklarations instansen:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

För Data Box-enhet Blob Storage anges slut punktens suffix i deklarations instansen:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
