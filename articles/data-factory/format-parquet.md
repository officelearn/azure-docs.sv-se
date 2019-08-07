---
title: Parquet-format i Azure Data Factory | Microsoft Docs
description: I det här avsnittet beskrivs hur du hanterar Parquet-format i Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 572547f4e22a4fcb63a030e64ca95a0b9d3eff00
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734488"
---
# <a name="parquet-format-in-azure-data-factory"></a>Parquet-format i Azure Data Factory

Följ den här artikeln när du vill **parsa Parquet-filerna eller skriva data i Parquet-format**. 

Parquet-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Parquet-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Data uppsättningens typ-egenskap måste anges till **Parquet**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds `location`under. **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |
| compressionCodec | Den komprimerings-codec som ska användas när du skriver till Parquet-filer. Vid läsning från Parquet-filer bestämmer Data Factory automatiskt komprimerings-codecen baserat på filens metadata.<br>De typer som stöds är "**none**", "**gzip**", "**fästfunktionen**" (standard) och "**LZO**". Obs! kopierings aktiviteten stöder för närvarande inte LZO. | Nej       |

> [!NOTE]
> Tomt utrymme i kolumn namn stöds inte för Parquet-filer.

Nedan visas ett exempel på en Parquet-datauppsättning på Azure Blob Storage:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Parquet-källan och Sink.

### <a name="parquet-as-source"></a>Parquet som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\*källa\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ egenskapen för kopierings aktivitets källan måste anges till **ParquetSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

### <a name="parquet-as-sink"></a>Parquet som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets ***\*mottagare\**** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Typ egenskapen för kopierings aktivitets källan måste anges till **ParquetSink**. | Ja      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="data-type-support"></a>Data typs stöd

Parquet komplexa data typer stöds för närvarande inte (t. ex. MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Använda egen värd Integration Runtime

> [!IMPORTANT]
> Om du inte kopierar Parquet-filer i **befintligt skick som är som är**integration runtime behöriga för den lokala datorn och data lager i molnet, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller OPENJDK** på din IR-dator. Se följande stycke med mer information.

För kopiering som körs på egen värd-IR med Parquet-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera *`JAVA_HOME`* system variabeln för openjdk.

- **Så här använder du JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera JVM. dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och ange system miljö variabeln JAVA_HOME.

> [!TIP]
> Om du kopierar data till/från Parquet-format med hjälp av självbetjäning integration Runtime och träffa fel som säger "ett fel uppstod vid aktivering av Java, meddelande: **Java. lang. OutOfMemoryError: Java heap-utrymme**" kan du lägga `_JAVA_OPTIONS` till en miljö variabel i en dator som är värd för IR med egen värd för att justera den minsta/högsta heap-storleken för JVM för att ge en sådan kopia och sedan köra pipelinen igen.

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabeln `_JAVA_OPTIONS` med värde. `-Xms256m -Xmx16g` Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda `Xmx` maximalt mängd minne. Som standard använder ADF den minsta 64 MB och Max 1G.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
