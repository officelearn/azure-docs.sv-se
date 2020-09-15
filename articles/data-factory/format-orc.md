---
title: ORC-format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar ORC-format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 3aa42d6060ecdd93dd97438a025c4f5e4f05ac52
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531737"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC-format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Orc-filerna eller skriva data i Orc-format**. 

ORC-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Orc**. | Yes      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` . **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Yes      |

Nedan visas ett exempel på en ORC-datauppsättning på Azure Blob Storage:

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

* Komplexa data typer stöds inte (STRUCT, MAP, LIST, UNION).
* Tomt utrymme i kolumn namnet stöds inte.
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-källan och Sink.

### <a name="orc-as-source"></a>ORC som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \* källa \* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **OrcSource**. | Yes      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | No       |

### <a name="orc-as-sink"></a>ORC som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \* mottagare \* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **OrcSink**. | Yes      |
| formatSettings | En grupp med egenskaper. Se **Orc Write Settings** Table nedan. |    No      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings` . **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | No       |

**Skriv inställningar för Orc** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **OrcWriteSettings**. | Yes                                                   |
| maxRowsPerFile | När du skriver data till en mapp kan du välja att skriva till flera filer och ange max rader per fil.  | No |
| fileNamePrefix | Gäller när `maxRowsPerFile` har kon figurer ATS.<br> Ange prefixet för fil namn när du skriver data till flera filer, vilket resulterade i det här mönstret: `<fileNamePrefix>_00000.<fileExtension>` . Om inget anges skapas prefixet för fil namn automatiskt. Den här egenskapen gäller inte när källan är filbaserad lagring eller [partition-alternativ-aktiverat data lager](copy-activity-performance-features.md).  | No |

## <a name="using-self-hosted-integration-runtime"></a>Använda egen värd Integration Runtime

> [!IMPORTANT]
> Om du inte kopierar ORC-filer **som du har**befogenhet att kopiera med egen värd integration runtime, t. ex. mellan lokala och molnbaserade data lager, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller openjdk** och **Microsoft Visual C++ 2010 Redistributable Package** på din IR-dator. Mer information finns i följande stycke.

För kopiering som körs på egen värd-IR med ORC-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera system variabeln *`JAVA_HOME`* för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och Ställ in system miljö variabeln JAVA_HOME därför.
- **Installera Visual c++ 2010 Redistributable Package**: Visual c++ 2010 Redistributable Package installeras inte med IR-installationer med egen värd. Du hittar den [här](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Om du kopierar data till/från ORC-format med hjälp av självbetjäning Integration Runtime och träffa fel som säger "ett fel uppstod vid anrop till Java, meddelande: **Java. lang. OutOfMemoryError: Java-heap-utrymme**", kan du lägga till en miljö variabel `_JAVA_OPTIONS` på den dator som är värd för den egna IR-enheten för att justera den minsta/högsta heap-storleken för JVM för att få en

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabeln `_JAVA_OPTIONS` med värde `-Xms256m -Xmx16g` . Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda maximalt `Xmx` mängd minne. Som standard använder ADF min 64 MB och Max 1G.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopieringsaktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
