---
title: Parquet-format i Azure Data Factory | Microsoft Docs
description: Det här avsnittet beskriver hur du arbetar med Parquet-format i Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144886"
---
# <a name="parquet-format-in-azure-data-factory"></a>Parquet-format i Azure Data Factory

Följ den här artikeln när du vill **parsa Parquet-filer eller skriva data i Parquet-format**. 

Parquet-format stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Filsystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), och [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Parquet-datauppsättningen.

| Egenskap          | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Type-egenskapen för datauppsättningen måste anges till **Parquet**. | Ja      |
| location         | Platsinställningar för filen eller filerna. Varje filbaserade anslutning har sin egen platstyp och stöds egenskaper under `location`. **Mer information finns i artikeln connector -> datauppsättning egenskapsavsnittet**. | Ja      |
| compressionCodec | Komprimerings-codec ska användas vid skrivning till Parquet-filer. Vid läsning från Parquet-filer, fastställa Data Factory automatiskt komprimerings-codec baserat på filens metadata.<br>Typer som stöds är ”**ingen**” ”,**gzip**” ”,**snappy**” (standard), och ”**lzo**”. Observera för närvarande Kopieringsaktivitet inte stöder LZO. | Nej       |

> [!NOTE]
> Tomt utrymme i kolumnnamn stöds inte för Parquet-filer.

Nedan visas ett exempel på Parquet-datauppsättningen på Azure Blob Storage:

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Parquet källa och mottagare.

### <a name="parquet-as-source"></a>Parquet som källa

Följande egenskaper stöds i kopieringsaktiviteten ***\*källa\**** avsnittet.

| Egenskap       | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Type-egenskapen för aktiviteten kopieringskälla måste anges till **ParquetSource**. | Ja      |
| storeSettings | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserade anslutningsprogrammet har sin egen Läs inställningar som stöds under `storeSettings`. **Mer information finns i artikeln connector -> Kopiera aktivitet egenskapsavsnittet**. | Nej       |

### <a name="parquet-as-sink"></a>Parquet som mottagare

Följande egenskaper stöds i kopieringsaktiviteten ***\*mottagare\**** avsnittet.

| Egenskap       | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Type-egenskapen för aktiviteten kopieringskälla måste anges till **ParquetSink**. | Ja      |
| storeSettings | En grupp egenskaper om hur du skriver data till ett datalager. Varje filbaserade anslutning har sin egen stöds skrivning inställningar under `storeSettings`. **Mer information finns i artikeln connector -> Kopiera aktivitet egenskapsavsnittet**. | Nej       |

## <a name="mapping-data-flow-properties"></a>Egenskaper för mappning av dataflöde

Få mer detaljerad information från [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappning dataflöde.

## <a name="data-type-support"></a>Stöd för datatypen

Parquet komplexa datatyper inte är för närvarande stöds (t.ex. MAP, LIST, STRUCT).

## <a name="using-self-hosted-integration-runtime"></a>Med hjälp av lokal Integration Runtime

> [!IMPORTANT]
> För att kopiera möjligheter med lokal Integration Runtime t.ex. mellan lokala och molnbaserade datalager, om du inte kopierar Parquet-filer **som – är**, måste du installera den **64-bitars JRE 8 (Java Runtime Environment) eller OpenJDK** på IR-datorn. Se följande stycke med mer information.

För kopiering som körs på lokal IR med Parquet-fil serialisering/deserialisering ADF söker efter Java runtime genom att först kontrollera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om inte hittas för det andra kontrollerar systemvariabeln *`JAVA_HOME`* för OpenJDK.

- **Att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den från [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Att använda OpenJDK**: den stöds sedan IR version 3.13. Paketet jvm.dll med alla andra krävs sammansättningar av OpenJDK i lokal IR-datorn och system för set-miljövariabeln JAVA_HOME därefter.

> [!TIP]
> Om du kopierar data till/från Parquet-format med hjälp av lokal Integration Runtime och tryck på fel som säger ”ett fel uppstod när java-anrop meddelande: **java.lang.OutOfMemoryError:Java heap utrymme**”, kan du lägga till en miljövariabel `_JAVA_OPTIONS` på den dator som är värd för lokal IR för att justera storleken på heap min/max för JVM att driva denna kopia, sedan kör pipelinen igen.

![Ange JVM heap-storlek för lokal IR](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabel `_JAVA_OPTIONS` med värdet `-Xms256m -Xmx16g`. Flaggan `Xms` anger inledande allokering minnespoolen för en Java Virtual Machine (JVM), medan `Xmx` anger den högsta mängd minne allokering poolen. Det innebär att du kommer igång JVM med `Xms` mängden minne och kommer att kunna använda högst `Xmx` mängden minne. Som standard ADF använda min 64MB och max 1G.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)