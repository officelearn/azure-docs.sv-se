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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416329"
---
# <a name="binary-format-in-azure-data-factory"></a>Binärt format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Binärt format stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

Du kan använda binär datauppsättning i [kopiera aktivitet,](copy-activity-overview.md) [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)eller [Ta bort aktivitet](delete-activity.md). När du använder binär datauppsättning tolkar ADF inte filinnehåll men behandlar det som det är. 

>[!NOTE]
>När du använder binär datauppsättning i kopieringsaktivitet kan du bara kopiera från binär datauppsättning till binär datauppsättning.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av binär datauppsättning.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Egenskapen Type property för datauppsättningen måste anges till **Binär**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`. **Se information i kopplingens artikel -> egenskaper för datauppsättning**. | Ja      |
| komprimering | Grupp med egenskaper för att konfigurera filkomprimering. Konfigurera det här avsnittet när du vill göra komprimering/dekompression under aktivitetskörningen. | Inga |
| typ | Komprimeringscodec som används för att läsa/skriva binära filer. <br>Tillåtna värden är **bzip2**, **gzip**, **deflate**, **ZipDeflate**. att använda när du sparar filen.<br>Observera att när du använder kopieringsaktivitet för att expandera ZipDeflate-filer och skriva till filbaserat `<path specified in dataset>/<folder named as source zip file>/`sink-datalager extraheras filerna till mappen: . | Inga       |
| nivå | Kompressionsförhållandet. Använd när datauppsättning används i Kopiera aktivitetsmottagare.<br>Tillåtna värden är **Optimal** eller **Snabbaste**.<br>- **Snabbast:** Komprimeringsåtgärden bör slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **Optimal**: Komprimeringsoperationen ska komprimeras optimalt, även om operationen tar längre tid att slutföra. Mer information finns i avsnittet [Komprimeringsnivå.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Inga       |

Nedan följer ett exempel på binär datauppsättning på Azure Blob Storage:

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av den binära källan och diskhon.

>[!NOTE]
>När du använder binär datauppsättning i kopieringsaktivitet kan du bara kopiera från binär datauppsättning till binär datauppsättning.

### <a name="binary-as-source"></a>Binär som källa

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **BinarySource**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

### <a name="binary-as-sink"></a>Binär som diskbänk

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopiaaktivitetskällan måste anges till **BinarySink**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)
