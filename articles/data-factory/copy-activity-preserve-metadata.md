---
title: 'Bevara metadata och ACL: er med kopierings aktivitet i Azure Data Factory'
description: 'Lär dig mer om hur du bevarar metadata och ACL: er under kopiering med kopierings aktivitet i Azure Data Factory.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357211"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Bevara metadata och ACL: er med kopierings aktivitet i Azure Data Factory

När du använder Azure Data Factory kopierings aktivitet för att kopiera data från källan till Sink, kan du i följande scenarier även bevara metadata och åtkomst kontrol listor tillsammans.

## <a name="preserve-metadata"></a>Bevara metadata för sjö migrering

När du migrerar data från ett data Lake till ett annat, inklusive [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), kan du välja att bevara filens metadata tillsammans med data.

Kopierings aktiviteten har stöd för att bevara följande attribut under data kopieringen:

- **Alla angivna metadata för kunden** 
- Och följande **fem inbyggda system egenskaper för data lagring**: `contentType`, `contentLanguage` (förutom Amazon S3), `contentEncoding`, `contentDisposition`, `cacheControl`.

När du kopierar filer som de är från Amazon S3/Azure Data Lake Storage Gen2/Azure blob till Azure Data Lake Storage Gen2/Azure Blob med binärformat kan du hitta alternativet **Spara** på fliken **Kopiera aktivitet** > **Inställningar** för aktivitets redigering eller sidan **Inställningar** i kopiera data verktyget.

![Bevara metadata för kopierings aktivitet](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Här är ett exempel på en kopierings aktivitets JSON-konfiguration (se `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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

## <a name="preserve-acls"></a>Bevara ACL: er från Data Lake Storage Gen1 till Gen2

När du uppgraderar från Azure Data Lake Storage Gen1 till Gen2 kan du välja att behålla åtkomst kontrol listorna för POSIX (ACL) tillsammans med datafiler. Mer information om åtkomst kontroll finns i [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md) och [åtkomst kontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Kopierings aktiviteten stöder bevarande av följande typer av ACL: er under data kopieringen. Du kan välja en eller flera typer:

- **ACL**: kopiera och bevara POSIX-åtkomst kontrol listor på filer och kataloger. Den kopierar alla befintliga ACL: er från källan till Sink. 
- **Ägare**: kopiera och bevara ägande användare av filer och kataloger. Super-User-åtkomst till Sink Data Lake Storage Gen2 krävs.
- **Grupp**: kopiera och bevara den ägande gruppen med filer och kataloger. Super-User-åtkomst till Sink Data Lake Storage Gen2 eller ägande användare (om ägande användare också är medlem i mål gruppen) krävs.

Om du väljer att kopiera från en mapp replikeras Data Factory ACL: er för den aktuella mappen och filerna och katalogerna under den, om `recursive` har angetts till true. Om du väljer att kopiera från en enskild fil kopieras ACL: erna på den filen.

>[!NOTE]
>När du använder ADF för att bevara ACL: er från Data Lake Storage Gen1 till Gen2 kommer befintliga ACL: er på Gen2's motsvarande mapp/filer att skrivas över.

>[!IMPORTANT]
>När du väljer att bevara ACL: er bör du se till att du beviljar hög tillräcklig behörighet för Data Factory att använda ditt mottagares Data Lake Storage Gen2-konto. Använd t. ex. autentisering av konto nycklar eller tilldela rollen Storage BLOB data-ägare till tjänstens huvud namn eller hanterad identitet.

När du konfigurerar källa som Data Lake Storage Gen1 med binärt format eller alternativ för binär kopia och handfat som Data Lake Storage Gen2 med binärformat eller alternativet binär kopia, kan du hitta alternativet **bevara** på sidan **Inställningar** i kopiera data-verktyget eller på fliken **Kopiera aktivitet** > **Inställningar** för redigering av aktiviteter.

![Data Lake Storage Gen1 bevara ACL-Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Här är ett exempel på en kopierings aktivitets JSON-konfiguration (se `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
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

Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Kopiera aktivitets prestanda](copy-activity-performance.md)
