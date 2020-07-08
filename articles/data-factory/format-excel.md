---
title: Excel-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar Excel-formatet i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670924"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Excel-filerna**. Azure Data Factory stöder både ". xls" och ". xlsx".

Excel-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md). Den stöds som källa men inte Sink.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Excel-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Obligatorisk |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Excel**.   | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . | Ja      |
| Inställda SheetName        | Namnet på Excel-kalkylbladet för att läsa data.                       | Ja      |
| intervall            | Cell området i det aktuella kalkyl bladet för att hitta de selektiva data, t. ex. `A3:H5` (en tabell från a3 till H5), `A3` (en tabell som börjar från cell A3), `A3:A3` (enkel cell). Om inget anges läser ADF-filen från hela kalkyl bladet som en tabell. | No       |
| firstRowAsHeader | Anger om den första raden i angivet kalkyl blad/intervall ska behandlas som en rubrik rad med kolumn namn.<br>Tillåtna värden är **True** och **false** (standard). | No       |
| nullValue        | Anger sträng representationen för null-värde. <br>Standardvärdet är en **tom sträng**. | No       |
| compressionCodec | Komprimerings-codec som används för att läsa Excel-filer. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **fästfunktionen**eller **lz4**. Standardvärdet är inte komprimerat. <br>**Obs!** kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate". <br>**Obs!** när du använder kopierings aktivitet för att expandera **ZipDeflate** -filer och skriva till filbaserat mottagar data lager, extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/` . | No       |
| compressionLevel | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Nedan visas ett exempel på en Excel-datauppsättning på Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Excel-källan.

### <a name="excel-as-source"></a>Excel som källa 

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \* källa \* *** .

| Egenskap      | Beskrivning                                                  | Obligatorisk |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **ExcelSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . | Nej       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
