---
title: Binärt format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar binärt format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416329"
---
# <a name="binary-format-in-azure-data-factory"></a>Binärt format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binärt format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

Du kan använda binär data uppsättning i [kopierings aktivitet](copy-activity-overview.md), [getMetaData aktivitet](control-flow-get-metadata-activity.md)eller [ta bort aktivitet](delete-activity.md). När du använder binär data uppsättning tolkar inte ADF fil innehållet, men behandlar det som det är. 

>[!NOTE]
>När du använder binär data mängd i kopierings aktivitet kan du bara kopiera från binär data uppsättning till binär data mängd.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av den binära data uppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Binary**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds `location`under. **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| komprimering | Grupp egenskaper för att konfigurera fil komprimering. Konfigurera det här avsnittet när du vill utföra komprimering/expandering under aktivitets körningen. | Nej |
| typ | Komprimerings-codec som används för att läsa/skriva binära filer. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**. att använda när du sparar filen.<br>Obs! när du använder kopierings aktivitet för att expandera ZipDeflate-filer och skriva till filbaserat mottagar data lager extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/`. | Nej       |
| nivå | Komprimerings förhållandet. Använd när dataset används i kopierings aktiviteten Sink.<br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nej       |

Nedan visas ett exempel på en binär data uppsättning på Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av den binära källan och mottagaren.

>[!NOTE]
>När du använder binär data mängd i kopierings aktivitet kan du bara kopiera från binär data uppsättning till binär data mängd.

### <a name="binary-as-source"></a>Binär som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \*källa\* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **BinarySource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

### <a name="binary-as-sink"></a>Binär som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \*mottagare\* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **BinarySink**. | Ja      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)
