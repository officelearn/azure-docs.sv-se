---
title: Avro-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar Avro-format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416355"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro-format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **tolka Avro-filerna eller skriva data i Avro-format**. 

Avro-format stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Datauppsättningens typegenskap måste anges till **Avro**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`. **Se information i kopplingens artikel -> egenskaper för datauppsättning**. | Ja      |
| avroKomprimeringCodec | Komprimeringscodec som ska användas när du skriver till Avro-filer. När du läser från Avro-filer bestämmer Data Factory automatiskt komprimeringscodec baserat på filens metadata.<br>Typer som stöds är "**inga**" (standard), "**deflate**", "**snappy**". Kopiera aktivitet stöder för närvarande inte Snappy när du läser/skriver Avro-filer. | Inga       |

> [!NOTE]
> Blanksteg i kolumnnamnet stöds inte för Avro-filer.

Nedan följer ett exempel på Avro-datauppsättning på Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-källan och diskhon.

### <a name="avro-as-source"></a>Avro som källa

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **AvroSource**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

### <a name="avro-as-sink"></a>Avro som diskbänk

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste vara av **avroSink**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

## <a name="data-type-support"></a>Stöd för datatyp

### <a name="copy-activity"></a>Kopieringsaktivitet
[Avro-komplexa datatyper](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, fackföreningar och fasta) i Kopiera aktivitet.

### <a name="data-flows"></a>Dataflöden
När du arbetar med Avro-filer i dataflöden kan du läsa och skriva komplexa datatyper, men se till att först rensa det fysiska schemat från datauppsättningen. I dataflöden kan du ange logiska projektions- och härledningskolumner som är komplexa strukturer och sedan mappa dessa fält automatiskt till en Avro-fil.

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
