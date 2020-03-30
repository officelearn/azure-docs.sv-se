---
title: Avgränsat textformat i Azure Data Factory
description: I det här avsnittet beskrivs hur du hanterar avgränsat textformat i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: f2e70a7b900ad918cda05ce34204e2de1e6e67ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830198"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Avgränsat textformat i Azure Data Factory

Följ den här artikeln när du vill **tolka de avgränsade textfilerna eller skriva data till avgränsat textformat**. 

Avgränsat textformat stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av den avgränsade textdatauppsättningen.

| Egenskap         | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Egenskapen Type property för datauppsättningen måste anges till **AvgränsadText**. | Ja      |
| location         | Platsinställningar för filen/filerna. Varje filbaserad koppling har sin egen platstyp och egenskaper som stöds under `location`.  | Ja      |
| columnDelimiter  | De tecken som används för att separera kolumner i en fil. För närvarande stöds multi-char-avgränsare endast för mappning av dataflöde men inte kopiera aktivitet. <br>Standardvärdet är **komma `,` **, När kolumn avgränsaren definieras som tom sträng, vilket innebär ingen avgränsare, tas hela raden som en enda kolumn. | Inga       |
| rowDelimiter     | Det enstaka tecknet eller "\r\n" som används för att separera rader i en fil.<br>Standardvärdet är något av följande värden **som ska läsas: ["\r\n", "\r", "\n"]** och **"\n" eller "\r\n" vid skrivning** genom att mappa dataflöde respektive Kopiera aktivitet. <br>När `rowDelimiter` är inställt på ingen avgränsare `columnDelimiter` (tom sträng), måste ställas in som ingen avgränsare (tom sträng) också, vilket innebär att behandla hela innehållet som ett enda värde. | Inga       |
| quoteChar        | Ett tecken som ska citera kolumnvärden om det innehåller kolumnavgränsare. <br>Standardvärdet är **dubbla citattecken** `"`. <br>För mappning `quoteChar` av dataflöde kan det inte vara en tom sträng. <br>För kopiera aktivitet, när `quoteChar` definieras som tom sträng, betyder det att det inte `escapeChar` finns någon citattecken och kolumnvärde inte citeras och används för att undkomma kolumnavgränsaren och sig själv. | Inga       |
| escapeChar       | Det enda teckenet för att undkomma citat inuti ett citerat värde.<br>Standardvärdet är **omvänt `\`snedstreck **. <br>För mappning `escapeChar` av dataflöde kan det inte vara en tom sträng. <br/>För kopieringsaktivitet, när `escapeChar` det definieras `quoteChar` som tom sträng, måste det också anges som tom sträng, och se till att alla kolumnvärden inte innehåller avgränsare. | Inga       |
| firstRowAsHeader | Anger om den första raden ska behandlas/skapas som en rubrikrad med namn på kolumner.<br>Tillåtna värden är **sanna** och **falska** (standard). | Inga       |
| nullValue        | Anger strängrepresentation av null-värde. <br>Standardvärdet är **tom sträng**. | Inga       |
| encodingName     | Kodningstypen som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Dataflöde för anteckningar stöder inte UTF-7-kodning. | Inga       |
| komprimeringCodec | Komprimeringscodec som används för att läsa/skriva textfiler. <br>Tillåtna värden är **bzip2,** **gzip**, **deflate**, **ZipDeflate**, **snappy**eller **lz4**. att använda när du sparar filen. <br>Kopiera aktivitet stöder för närvarande inte "snappy" & "lz4", och mappningsdataflödet stöder inte "ZipDeflate". <br>Observera att när du använder kopieringsaktivitet för att expandera ZipDeflate-filer och skriva till filbaserat `<path specified in dataset>/<folder named as source zip file>/`sink-datalager extraheras filerna till mappen: . | Inga       |
| komprimeringNivå | Kompressionsförhållandet. <br>Tillåtna värden är **Optimal** eller **Snabbaste**.<br>- **Snabbast:** Komprimeringsåtgärden bör slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **Optimal**: Komprimeringsoperationen ska komprimeras optimalt, även om operationen tar längre tid att slutföra. Mer information finns i avsnittet [Komprimeringsnivå.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Inga       |

Nedan följer ett exempel på avgränsad textdatauppsättning på Azure Blob Storage:

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av den avgränsade textkällan och diskhon.

### <a name="delimited-text-as-source"></a>Avgränsad text som källa 

Följande egenskaper stöds i källavsnittet för *** \*kopieringsaktivitet.\* ***

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Egenskapen Type property för kopians aktivitetskälla måste anges till **AvgränsadTextSource**. | Ja      |
| formateraInställningar | En grupp egenskaper. Se tabellen **Avgränsad textläsningsinställning** nedan. | Inga       |
| storeInställningar  | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`läsinställningar som stöds under . | Inga       |

Inställningar för **avgränsad textläsning** som stöds under `formatSettings`:

| Egenskap      | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typen av formatInställningar måste anges till **AvgränsadeTextReadSettings**. | Ja      |
| skipLineCount | Anger hur många **rader som inte är tomma** att hoppa över när du läser data från indatafiler. <br>Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. | Inga       |

### <a name="delimited-text-as-sink"></a>Avgränsad text som diskbänk

Följande egenskaper stöds i avsnittet kopiera *** \*aktivitetsmottagare.\* ***

| Egenskap       | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Egenskapen Type property för kopians aktivitetskälla måste anges till **AvgränsadTextSink**. | Ja      |
| formateraInställningar | En grupp egenskaper. Se tabellen Inställningar för **avgränsad textskrivning** nedan. |          |
| storeInställningar  | En grupp egenskaper för hur du skriver data till ett datalager. Varje filbaserad anslutning har sina egna `storeSettings`skrivinställningar som stöds under .  | Inga       |

Inställningar **för avgränsad text skriv som** stöds under `formatSettings`:

| Egenskap      | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typen av formatInställningar måste anges till **AvgränsadeTextWriteSettings**. | Ja                                                   |
| filUtstension | Filtillägget som används för att namnge `.csv`utdatafilerna, `.txt`t.ex. Det måste anges när `fileName` den inte anges i datauppsättningen Avgränsad text. När filnamnet konfigureras i utdatauppsättningen används det som sink-filnamn och filtilläggsinställningen ignoreras.  | Ja när filnamnet inte anges i utdatauppsättningen |

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

Lär dig information från [källomvandling](data-flow-source.md) och [sink-omvandling](data-flow-sink.md) i mappning av dataflöde.

## <a name="next-steps"></a>Nästa steg

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
