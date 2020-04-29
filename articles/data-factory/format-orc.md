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
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417733"
---
# <a name="orc-format-in-azure-data-factory"></a>ORC-format i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa Orc-filerna eller skriva data i Orc-format**. 

ORC-formatet stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av ORC-datauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **Orc**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds `location`under. **Se information i avsnittet kopplings artikel – egenskaper för > data uppsättning**. | Ja      |

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

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \*källa\* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **OrcSource**. | Ja      |
| storeSettings | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

### <a name="orc-as-sink"></a>ORC som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \*mottagare\* *** .

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ egenskapen för kopierings aktivitets källan måste anges till **OrcSink**. | Ja      |
| storeSettings | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`. **Se information i kopplings artikeln – > avsnittet Egenskaper för kopierings aktivitet**. | Nej       |

## <a name="using-self-hosted-integration-runtime"></a>Använda egen värd Integration Runtime

> [!IMPORTANT]
> Om du inte kopierar ORC-filer **som du har**befogenhet att kopiera med egen värd integration runtime, t. ex. mellan lokala och molnbaserade data lager, måste du installera **64-bitars JRE 8 (Java Runtime Environment) eller openjdk** och **Microsoft Visual C++ 2010 Redistributable Package** på din IR-dator. Mer information finns i följande stycke.

För kopiering som körs på egen värd-IR med ORC-filserialisering/deserialisering, hittar ADF Java-körningen genom att först kontrol lera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om det inte hittas, och sedan kontrol lera *`JAVA_HOME`* system variabeln för openjdk.

- **För att använda JRE**: 64-bitars IR kräver 64-bitars JRE. Du hittar den [här](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Om du vill använda openjdk**: stöds den sedan IR version 3,13. Paketera JVM. dll med alla andra nödvändiga sammansättningar av OpenJDK till IR-datorn med egen värd och ange system miljö variabeln JAVA_HOME.
- **Installera Visual c++ 2010 Redistributable Package**: Visual c++ 2010 Redistributable Package installeras inte med IR-installationer med egen värd. Du hittar den [här](https://www.microsoft.com/download/details.aspx?id=14632).

> [!TIP]
> Om du kopierar data till/från ORC-format med hjälp av självbetjäning Integration Runtime och träffa fel som säger "ett fel uppstod vid anrop till Java, meddelande: **Java. lang. OutOfMemoryError: Java-heap-utrymme**", kan `_JAVA_OPTIONS` du lägga till en miljö variabel på den dator som är värd för den egna IR-enheten för att justera den minsta/högsta heap-storleken för JVM för att få en

![Ange JVM heap-storlek för IR med egen värd](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: Ange variabeln `_JAVA_OPTIONS` med `-Xms256m -Xmx16g`värde. Flaggan `Xms` anger den första poolen för minnesallokering för en Java Virtual Machine (JVM), medan `Xmx` anger den maximala poolen för minnesallokering. Det innebär att JVM startas med `Xms` mängden minne och kommer att kunna använda maximalt `Xmx` mängd minne. Som standard använder ADF min 64 MB och Max 1G.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
