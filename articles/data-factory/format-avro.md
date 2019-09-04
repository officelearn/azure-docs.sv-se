---
title: Avro-format i Azure Data Factory | Microsoft Docs
description: I det här avsnittet beskrivs hur du hanterar Avro-format i Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c1f55da2f1aada8c7e4a0d979b2e38e6782e564c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294235"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro-format i Azure Data Factory

Följ den här artikeln när du vill **parsa Avro-filerna eller skriva data i Avro-format**. 

Avro-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Data uppsättningens typ-egenskap måste anges till **Avro**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds `location`under. **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| avroCompressionCodec | Den komprimerings-codec som ska användas när du skriver till Avro-filer. Vid läsning från Avro-filer bestämmer Data Factory automatiskt komprimerings-codecen baserat på filens metadata.<br>De typer som stöds är "**none**" (standard),**DEFLATE**","**fästning**". | Nej       |

> [!NOTE]
> Tomt utrymme i kolumn namn stöds inte för Avro-filer.

Nedan visas ett exempel på en Avro-datauppsättning på Azure Blob Storage:

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Avro-källan och Sink.

### <a name="avro-as-source"></a>Avro som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\*källa\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ egenskapen för kopierings aktivitets källan måste anges till **AvroSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

### <a name="avro-as-sink"></a>Avro som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\*mottagare\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ egenskapen för kopierings aktivitets källan måste anges till **AvroSink**. | Ja      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

## <a name="data-type-support"></a>Data typs stöd

Avro [komplexa data typer](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, unioner och fasta).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
