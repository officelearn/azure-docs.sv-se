---
title: Bevara metadata och ACL:er med hjälp av kopieringsaktivitet i Azure Data Factory
description: Lär dig mer om hur du bevarar metadata och ACL:er under kopiering med hjälp av kopieringsaktivitet i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414156"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Bevara metadata och ACL:er med hjälp av kopieringsaktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du använder Azure Data Factory-kopieringsaktivitet för att kopiera data från källa till diskho, i följande scenarier kan du också bevara metadata och ACL:er tillsammans.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Bevara metadata för sjömigrering

När du migrerar data från en datasjö till en annan, inklusive [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md)och [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)kan du välja att bevara filmetadata tillsammans med data.

Kopieringsaktiviteten stöder bevarande av följande attribut under datakopiering:

- **Alla kundspecificerade metadata** 
- Och följande **fem datalager inbyggda systemegenskaper:** `contentType` `contentLanguage` , (med `contentEncoding` `contentDisposition`undantag `cacheControl`för Amazon S3), , , .

När du kopierar filer enligt Amazon S3/Azure Data Lake Storage Gen2/Azure Blob till Azure Data Lake Storage Gen2/Azure Blob med binärt format, hittar du alternativet **Bevara** på fliken Kopiera**aktivitetsinställningar** **Copy Activity** > för aktivitetsförfattare eller sidan **Inställningar** i Kopiera dataverktyg.

![Kopiera metadata för att bevara aktivitet](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Här är ett exempel på kopieringsaktivitet `preserve`JSON-konfiguration (se): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Bevara ACL:er från DataSjölagring Gen1/Gen2 till Gen2

När du uppgraderar från Azure Data Lake Storage Gen1 till Gen2 eller kopierar data mellan ADLS Gen2 kan du välja att bevara POSIX-åtkomstkontrollistorna (ACL: er) tillsammans med datafiler. Mer information om åtkomstkontroll finns [i Åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) och [Åtkomstkontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Kopieringsaktiviteten stöder bevarande av följande typer av ACL:er under datakopiering. Du kan välja en eller flera typer:

- **ACL**: Kopiera och bevara POSIX-åtkomstkontrollistor på filer och kataloger. Den kopierar alla befintliga ACL:er från källa till diskho. 
- **Ägare**: Kopiera och bevara den ägande användaren av filer och kataloger. Superanvändaråtkomst till sink Data Lake Storage Gen2 krävs.
- **Grupp**: Kopiera och bevara den ägande gruppen av filer och kataloger. Superanvändaråtkomst till sink Data Lake Storage Gen2 eller den ägande användaren (om den ägande användaren också är medlem i målgruppen) krävs.

Om du anger att kopiera från en mapp replikeras ACL:erna för den angivna `recursive` mappen och de filer och kataloger som anges under den, om den är true. Om du anger att kopiera från en enda fil kopieras ACL:erna på den filen.

>[!NOTE]
>När du använder ADF för att bevara ADF för att bevara ADF från Data Lake Storage Gen1/Gen2 till Gen2 skrivs de befintliga ACL:erna på sink Gen2:s motsvarande mapp/filer över.

>[!IMPORTANT]
>När du väljer att bevara ACL:er kontrollerar du att du ger tillräckligt hög behörighet för att Data Factory ska fungera mot ditt sink Data Lake Storage Gen2-konto. Använd till exempel kontonyckelautentisering eller tilldela rollen Lagringsblobbdataägare till tjänstens huvudnamn eller hanterade identitet.

När du konfigurerar källan som DataSjölagring Gen1/Gen2 med binärt format eller alternativet binär kopia och sjunker som Datasjölagringsgen2 med binärt format eller alternativet binär kopia, hittar du alternativet **Bevara** på sidan **Inställningar** i Kopiera dataverktyg eller på fliken Kopiera > **aktivitetsinställningar** för aktivitetsförfattande. **Copy Activity**

![Data Lake Storage Gen1/Gen2 till Gen2 Bevara ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Här är ett exempel på kopieringsaktivitet `preserve`JSON-konfiguration (se): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Nästa steg

Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)
