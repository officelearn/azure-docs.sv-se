---
title: Avgränsat text format i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar avgränsat text format i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 77d2daf3fa17632d8a1c633c23815e0035e45481
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931260"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Avgränsat text format i Azure Data Factory

Följ den här artikeln när du vill **parsa de avgränsade textfilerna eller skriva data i avgränsat text format**. 

Avgränsat text format stöds för följande anslutningar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure-File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av den avgränsade text data uppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Data uppsättningens typ-egenskap måste anges till **DelimitedText**. | Ja      |
| location         | Plats inställningar för filen/filerna. Varje filbaserad koppling har en egen plats typ och egenskaper som stöds under `location`.  | Ja      |
| columnDelimiter  | De/tecknen som används för att avgränsa kolumner i en fil. För närvarande stöds inte Multi-char-avgränsare för att mappa data flöde, men inte kopiera aktivitet. <br>Standardvärdet är **kommatecken `,`** när kolumn avgränsaren definieras som en tom sträng, vilket innebär att ingen avgränsare tas hela raden som en enda kolumn. | Nej       |
| rowDelimiter     | Det enstaka tecknen eller "\r\n" som används för att avgränsa rader i en fil.<br>Standardvärdet är något av följande värden **vid läsning: ["\r\n", "\r", "\n"]** och **"\n" eller "\r\n" vid skrivning** genom att mappa data flöde och kopiera aktivitet. <br>När `rowDelimiter` har angetts till ingen avgränsare (tom sträng), måste `columnDelimiter` anges som ingen avgränsare (tom sträng), vilket innebär att hela innehållet behandlas som ett enda värde. | Nej       |
| quoteChar        | Det enkla tecknet för att citera kolumn värden om det innehåller kolumn avgränsare. <br>Standardvärdet är **dubbla citat tecken** `"`. <br>`quoteChar` kan inte vara en tom sträng för att mappa data flödet. <br>När `quoteChar` definieras som en tom sträng i kopierings aktiviteten, betyder det att det inte finns något citat tecken och kolumnvärdet inte är citerat, och `escapeChar` används för att undanta kolumn avgränsaren och sig själv. | Nej       |
| escapeChar       | Det enda tecken som ska Escape-citat innanför ett citerat värde.<br>Standardvärdet är **omvänt snedstreck `\`** . <br>`escapeChar` kan inte vara en tom sträng för att mappa data flödet. <br/>För kopierings aktiviteten, när `escapeChar` definieras som en tom sträng, måste `quoteChar` anges som en tom sträng, och i så fall se till att alla kolumn värden inte innehåller avgränsare. | Nej       |
| firstRowAsHeader | Anger om du vill behandla/skapa den första raden som en rubrik rad med kolumn namn.<br>Tillåtna värden är **True** och **false** (standard). | Nej       |
| nullValue        | Anger sträng representationen för null-värde. <br>Standardvärdet är en **tom sträng**. | Nej       |
| encodingName     | Kodnings typen som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Data flödet för antecknings mappning stöder inte UTF-7-kodning. | Nej       |
| compressionCodec | Komprimerings-codec som används för att läsa/skriva textfiler. <br>Tillåtna värden är **bzip2**, **gzip**, **DEFLATE**, **ZipDeflate**, **fästfunktionen**eller **lz4**. att använda när du sparar filen. <br>Obs! kopierings aktiviteten stöder för närvarande inte "fästfunktionen" & "lz4" och kart data flödet stöder inte "ZipDeflate". <br>Obs! när du använder kopierings aktivitet för att expandera ZipDeflate-filer och skriva till filbaserat mottagar data lager extraheras filerna till mappen: `<path specified in dataset>/<folder named as source zip file>/`. | Nej       |
| compressionLevel | Komprimerings förhållandet. <br>Tillåtna värden är **optimalt** eller **snabbast**.<br>- **snabbast:** komprimerings åtgärden bör utföras så snart som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **optimalt**: komprimerings åtgärden bör komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i [komprimeringsnivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) avsnittet. | Nej       |

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
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av avgränsad text källa och mottagare.

### <a name="delimited-text-as-source"></a>Avgränsad text som källa 

Följande egenskaper stöds i avsnittet Kopiera aktivitet ***\*käll\**** .

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSource**. | Ja      |
| formatSettings | En grupp med egenskaper. Läs **Inställningar för avgränsad text** i tabellen nedan. | Nej       |
| storeSettings  | En grupp egenskaper för att läsa data från ett data lager. Varje filbaserad koppling har sina egna Läs inställningar som stöds under `storeSettings`. | Nej       |

**Läs inställningar för avgränsad text** som stöds under `formatSettings`:

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextReadSetting**. | Ja      |
| skipLineCount | Anger hur många **icke-tomma** rader att hoppa över vid läsning av data från indatafiler. <br>Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. | Nej       |

### <a name="delimited-text-as-sink"></a>Avgränsad text som mottagare

Följande egenskaper stöds i avsnittet kopierings aktivitet ***\*mottagare\**** .

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Typ egenskapen för kopierings aktivitets källan måste anges till **DelimitedTextSink**. | Ja      |
| formatSettings | En grupp med egenskaper. Se tabellen med **avgränsade text skriv inställningar** nedan. |          |
| storeSettings  | En grupp egenskaper för hur du skriver data till ett data lager. Varje filbaserad koppling har sina egna Skriv inställningar som stöds under `storeSettings`.  | Nej       |

**Inställningar för avgränsad text** som stöds under `formatSettings`:

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen för formatSettings måste anges till **DelimitedTextWriteSetting**. | Ja                                                   |
| fileExtension | Fil namns tillägget som används för att namnge utdatafilerna, t. ex. `.csv``.txt`. Det måste anges när `fileName` inte anges i data uppsättningen för utdata DelimitedText. | Ja när fil namnet inte anges i data uppsättningen för utdata |

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Lär dig mer om omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flödet.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
