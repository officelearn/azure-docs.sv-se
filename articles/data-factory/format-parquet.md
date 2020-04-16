---
title: Parkettformat i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar parkettformat i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 223b1b996b82acaa753eb55723e251dc5901bbec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417716"
---
# <a name="parquet-format-in-azure-data-factory"></a>Parkettformat i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **tolka parettfilerna eller skriva data i Parkettformat**. 

Parkettformat stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av parettdatauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Typegenskapen för datauppsättningen måste ställas in på **Parkett**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`. **Se information i kopplingens artikel -> egenskaper för datauppsättning**. | Ja      |
| komprimeringCodec | Komprimeringscodec att använda när du skriver till Parquet filer. När du läser från parquet filer, Data Fabriker automatiskt bestämma komprimering codec baserat på filen metadata.<br>Typer som stöds är "**ingen**", "**gzip**", "**snappy**" (standard) och "**lzo**". Observera för närvarande Kopiera aktivitet stöder inte LZO när läsa / skriva Parquet filer. | Inga       |

> [!NOTE]
> Blanksteg i kolumnnamnet stöds inte för parettfiler.

Nedan följer ett exempel på Parkettdatauppsättning på Azure Blob Storage:

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av parkettkällan och diskbänken.

### <a name="parquet-as-source"></a>Parkett som källa

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopiera aktivitetskällan måste ställas in på **ParquetSource**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

### <a name="parquet-as-sink"></a>Parkett som diskbänk

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typegenskapen för kopieringsaktivitetskällan måste ställas in på **Parkettsink**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

Lär dig information från [källomvandling](data-flow-source.md) och [sink-omvandling](data-flow-sink.md) i mappning av dataflöde.

## <a name="data-type-support"></a>Stöd för datatyp

Parkettkomplexa datatyper stöds för närvarande inte (t.ex.

## <a name="using-self-hosted-integration-runtime"></a>Använda självvärd för integrationskörning

> [!IMPORTANT]
> För kopiering som styrs av Självvärderade Integration Runtime, t.ex. **as-is** **64-bit JRE 8 (Java Runtime Environment) or OpenJDK** **Microsoft Visual C++ 2010 Redistributable Package** Kontrollera följande stycke med mer information.

För kopia som körs på Self-hosted IR med Parquet fil serialisering / deserialization, ADF lokaliserar Java runtime genom att först kontrollera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om inte hittas, andra kontroll systemvariabel *`JAVA_HOME`* för OpenJDK.

- **För att använda JRE:** 64-bitars IR kräver 64-bitars JRE. Du hittar den [härifrån.](https://go.microsoft.com/fwlink/?LinkId=808605)
- **Så här använder du OpenJDK**: Det stöds sedan IR version 3.13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK i självvärdad IR-dator och ställ in systemmiljövariabeln JAVA_HOME i enlighet med detta.
- **Så här installerar du Visual C++ 2010 Redistributable Package:** Visual C++ 2010 Redistributable Package är inte installerat med självvärderade IR-installationer. Du hittar den [härifrån.](https://www.microsoft.com/download/details.aspx?id=14632)

> [!TIP]
> Om du kopierar data till/från Parquet-format med självvärderade Integration Runtime och trycker på fel som säger "Ett fel uppstod när du anropar java, `_JAVA_OPTIONS` meddelande: **java.lang.OutOfMemoryError:Java heap space**", kan du lägga till en miljövariabel i maskinen som är värd för den självvärderade IR för att justera min / max heap-storleken för JVM för att ge en sådan kopia och kör sedan om pipelinen.

![Ange JVM-heap-storlek på självvärd ir](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: ange `_JAVA_OPTIONS` variabel `-Xms256m -Xmx16g`med värde . Flaggan `Xms` anger den första minnesallokeringspoolen för en `Xmx` Java Virtual Machine (JVM), medan den anger den maximala minnesallokeringspoolen. Detta innebär att JVM `Xms` kommer att startas med mängden minne `Xmx` och kommer att kunna använda maximalt mängd minne. Som standard använder ADF min 64 MB och max 1G.

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
