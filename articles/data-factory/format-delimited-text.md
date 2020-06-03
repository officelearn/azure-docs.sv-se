---
title: Avgränsat text format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar avgränsat text format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: jingwang
ms.openlocfilehash: 1b32685aa060363d00f1566e009beee36bbf9680
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298558"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Avgränsat text format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Följ den här artikeln när du vill **parsa de avgränsade textfilerna eller skriva data i avgränsat text format**. 

Avgränsat text format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av den avgränsade text data uppsättningen.

| Egenskap         | Beskrivning                                                  | Obligatorisk |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **DelimitedText**. | Yes      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har sin egen plats typ och de egenskaper som stöds under `location` .  | Yes      |
| columnDelimiter  | De/tecknen som används för att avgränsa kolumner i en fil. <br>Standardvärdet är **kommatecken `,` **. När kolumn avgränsaren definieras som en tom sträng, vilket innebär att ingen avgränsare görs, tas hela raden som en enda kolumn.<br>För närvarande stöds inte kolumn avgränsare som tom sträng eller flera tecken för att mappa data flöde, men inte kopierings aktivitet.  | No       |
| rowDelimiter     | Det enstaka tecknen eller "\r\n" som används för att avgränsa rader i en fil. <br>Standardvärdet är något av följande värden **vid läsning: ["\r\n", "\r", "\n"]** och **"\n" eller "\r\n" vid skrivning** genom att mappa data flöde och kopiera aktivitet. <br>När rad avgränsaren har angetts till ingen avgränsare (tom sträng), måste kolumn avgränsaren anges som ingen avgränsare (tom sträng), vilket innebär att hela innehållet ska behandlas som ett enda värde.<br>För närvarande stöds inte rad avgränsare som en tom sträng för data flöde för mappning, men inte kopierings aktivitet. | No       |
| quoteChar        | Det enkla tecknet för att citera kolumn värden om det innehåller kolumn avgränsare. <br>Standardvärdet är **dubbla citat tecken** `"` . <br>För att mappa data flödet `quoteChar` får inte vara en tom sträng. <br>För kopierings aktiviteten, när `quoteChar` definieras som en tom sträng, innebär det att det inte finns något citat tecken och kolumnvärdet inte är citerat, och `escapeChar` används för att undanta kolumn avgränsaren och sig själv. | No       |
| escapeChar       | Det enda tecken som ska Escape-citat innanför ett citerat värde.<br>Standardvärdet är **omvänt snedstreck `\` **. <br>För att mappa data flödet `escapeChar` får inte vara en tom sträng. <br/>För kopierings aktiviteten, när `escapeChar` definieras som en tom sträng, `quoteChar` måste även anges som en tom sträng, och i så fall se till att alla kolumn värden inte innehåller avgränsare. | No       |
| firstRowAsHeader | Anger om du vill behandla/skapa den första raden som en rubrik rad med kolumn namn.<br>Tillåtna värden är **True** och **false** (standard). | No       |
| nullValue        | Anger sträng representationen för null-värde. <br>Standardvärdet är en **tom sträng**. | No       |
| encodingName     | Kodnings typen som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Data flödet för antecknings mappning stöder inte UTF-7-kodning. | No       |
| compressionCodec | Komprimerings-codec som används för att läsa/skriva textfiler. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **fästfunktionen**eller **lz4**. Standardvärdet är inte komprimerat. <br>**Obs!** kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate". <br>**Observera** att när du använder kopierings aktivitet för att expandera **ZipDeflate** -fil (er) och skriva till filbaserat mottagar data lager, extraheras filerna som standard till mappen: `<path specified in dataset>/<folder named as source zip file>/` , Använd `preserveZipFileNameAsFolder` på [kopierings aktivitets källan](#delimited-text-as-source) för att kontrol lera om zip-filnamnet ska bevaras i mappstrukturen. | No       |
| compressionLevel | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **Snabbast:** Komprimerings åtgärden bör utföras så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt.<br>- **Optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i avsnittet [komprimerings nivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | No       |

Nedan visas ett exempel på en avgränsad text uppsättning på Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av avgränsad text källa och mottagare.

### <a name="delimited-text-as-source"></a>Avgränsad text som källa 

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \* källa \* *** .

| Egenskap       | Beskrivning                                                  | Obligatorisk |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSource**. | Yes      |
| formatSettings | En grupp med egenskaper. Läs **Inställningar för avgränsad text** i tabellen nedan. | No       |
| storeSettings  | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings` . | No       |

**Läs inställningar för avgränsad text** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Obligatorisk |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextReadSettings**. | Yes      |
| skipLineCount | Anger antalet **icke-tomma** rader som ska hoppas över vid läsning av data från indatafiler. <br>Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. | No       |
| compressionProperties | En grupp egenskaper för hur man dekomprimerar data för en angiven komprimerings-codec. | No       |
| preserveZipFileNameAsFolder<br>(*under `compressionProperties` *) | Gäller när indata-dataset konfigureras med **ZipDeflate** -komprimering. Anger om käll filens zip-filnamn ska bevaras som mappstruktur under kopieringen. Om värdet är true (standard), Data Factory skriver zippade filer till `<path specified in dataset>/<folder named as source zip file>/` . När värdet är false skriver data Factory zippade filer direkt till `<path specified in dataset>` .  | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Avgränsad text som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets *** \* mottagare \* *** .

| Egenskap       | Beskrivning                                                  | Obligatorisk |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSink**. | Yes      |
| formatSettings | En grupp med egenskaper. Se tabellen med **avgränsade text skriv inställningar** nedan. |          |
| storeSettings  | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings` .  | No       |

**Inställningar för avgränsad text** som stöds under `formatSettings` :

| Egenskap      | Beskrivning                                                  | Obligatorisk                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextWriteSettings**. | Yes                                                   |
| fileExtension | Fil namns tillägget som används för att namnge utdatafilerna, `.csv` t. ex.. `.txt` Det måste anges när `fileName` har inte angetts i data uppsättningen utgående DelimitedText. När fil namnet har kon figurer ATS i data uppsättningen för utdata används den som mottagar fil namn och fil namns inställningen ignoreras.  | Ja när fil namnet inte anges i data uppsättningen för utdata |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
