---
title: ORC-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar ORC-format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597498"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC-format i Azure Data Factory

Följ den här artikeln när du vill **tolka ORC-filerna eller skriva data i ORC-format**. 

ORC-format stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Datauppsättningens typegenskap måste anges till **Orc**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`. **Se information i kopplingens artikel -> egenskaper för datauppsättning**. | Ja      |

Nedan följer ett exempel på ORC-datauppsättning på Azure Blob Storage:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

Observera följande punkter:

* Komplexa datatyper stöds inte (STRUCT, MAP, LIST, UNION).
* Blanksteg i kolumnnamnet stöds inte.
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-källan och diskhon.

### <a name="orc-as-source"></a>ORC som källa

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **OrcSource**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

### <a name="orc-as-sink"></a>ORC som diskbänk

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Egenskapen Type property för kopians aktivitetskälla måste anges till **OrcSink**. | Ja      |
| storeInställningar | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under . **Se information i kopplingens artikel -> Kopiera aktivitetsegenskaper avsnitt**. | Inga       |

## <a name="using-self-hosted-integration-runtime"></a>Använda självvärd för integrationskörning

> [!IMPORTANT]
> För kopiering som styrs av självvärderade integrationskörning, t.ex. **as-is** **64-bit JRE 8 (Java Runtime Environment) or OpenJDK** **Microsoft Visual C++ 2010 Redistributable Package** Kontrollera följande stycke med mer information.

För kopiering som körs på Självvärd iR med ORC-fil serialisering /deserialization, ADF lokaliserar Java runtime genom att först kontrollera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om inte hittas, andra kontroll systemvariabel *`JAVA_HOME`* för OpenJDK.

- **För att använda JRE:** 64-bitars IR kräver 64-bitars JRE. Du hittar den [härifrån.](https://go.microsoft.com/fwlink/?LinkId=808605)
- **Så här använder du OpenJDK**: Det stöds sedan IR version 3.13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK i självvärdad IR-dator och ställ in systemmiljövariabeln JAVA_HOME i enlighet med detta.
- **Så här installerar du Visual C++ 2010 Redistributable Package:** Visual C++ 2010 Redistributable Package är inte installerat med självvärderade IR-installationer. Du hittar den [härifrån.](https://www.microsoft.com/download/details.aspx?id=14632)

> [!TIP]
> Om du kopierar data till/från ORC-format med självvärderade Integration Runtime och slår fel med texten "Ett fel uppstod när du anropar java, meddelande: **java.lang.OutOfMemoryError:Java heap space**", kan du lägga till en miljövariabel `_JAVA_OPTIONS` i datorn som är värd för den självvärderade IR för att justera min/max heap-storleken för JVM för att ge en sådan kopia och kör sedan om pipelinen.

![Ange JVM-heap-storlek på självvärd ir](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: ange `_JAVA_OPTIONS` variabel `-Xms256m -Xmx16g`med värde . Flaggan `Xms` anger den första minnesallokeringspoolen för en `Xmx` Java Virtual Machine (JVM), medan den anger den maximala minnesallokeringspoolen. Detta innebär att JVM `Xms` kommer att startas med mängden minne `Xmx` och kommer att kunna använda maximalt mängd minne. Som standard använder ADF min 64 MB och max 1G.

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
