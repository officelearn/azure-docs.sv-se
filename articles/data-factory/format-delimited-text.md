---
title: Avgränsat textformat i Azure Data Factory | Microsoft Docs
description: Det här avsnittet beskriver hur du arbetar med avgränsat textformat i Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 4e365ef01b8c7a89d61efad25bd18943e7b2d1a4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64877975"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Avgränsat textformat i Azure Data Factory

Följ den här artikeln när du vill **parsa avgränsad text-filerna eller skriva data till avgränsat textformat**. 

Avgränsat textformat stöds för följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Filsystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), och [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av datauppsättningen avgränsad text.

| Egenskap          | Beskrivning                                                  | Krävs |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Type-egenskapen för datauppsättningen måste anges till **DelimitedText**. | Ja      |
| location         | Platsinställningar för filen eller filerna. Varje filbaserade anslutning har sin egen platstyp och stöds egenskaper under `location`. **Mer information finns i artikeln connector -> datauppsättning egenskapsavsnittet**. | Ja      |
| columnDelimiter  | Tecken som används för att avgränsa kolumner i en fil. För närvarande stöds endast flera char avgränsare för mappning av dataflöde men inte Kopieringsaktivitet. <br>Standardvärdet är **kommatecken `,`** när kolumnavgränsare definieras som tom sträng, vilket innebär att ingen avgränsare hela raden tas den som en enda kolumn. | Nej       |
| rowDelimiter     | Enskilt tecken eller ”\r\n” används för att avgränsa rader i en fil.<br>Standardvärdet är något av följande värden **vid läsning: [”\r\n”, ”\r”, ”\n”]**, och **”\n” eller ”\r\n” vid skrivning till** genom att mappa Data flödar och kopiera aktivitet respektive. <br>När `rowDelimiter` är inställd på Ingen avgränsare (tom sträng), den `columnDelimiter` måste anges som inte har avgränsare (tom sträng) som, vilket innebär att behandla hela innehållet som ett enda värde. | Nej       |
| quoteChar        | Det enskilda tecknet citera kolumnvärdena om den innehåller en avgränsare för kolumnen. <br>Standardvärdet är **dubbla citattecken** `"`. <br>För mappning av dataflöde `quoteChar` får inte vara en tom sträng. <br>För kopieringsaktiviteten när `quoteChar` definieras som tom sträng, innebär det att det finns inga citattecken char och är inte av citattecken kolumnvärde, och `escapeChar` används för att undvika Kolumnavgränsare och sig själv. | Nej       |
| escapeChar       | Det enskilda tecknet att undvika citattecken inuti ett citerade värde.<br>Standardvärdet är **omvänt snedstreck `\`** . <br>För mappning av dataflöde `escapeChar` får inte vara en tom sträng. <br/>För kopieringsaktiviteten när `escapeChar` definieras som tom sträng, den `quoteChar` måste vara som samt tom sträng, kontrollera då alla kolumnvärdena inte innehåller avgränsare. | Nej       |
| firstRowAsHeader | Anger om du vill behandla den första raden/Kontrollera som en rubrikrad om du med namnen på kolumnerna.<br>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej       |
| nullValue        | Anger den sträng som innehåller null-värde. <br>Standardvärdet är **tom sträng**. | Nej       |
| encodingName     | Kodningstyp som används för att läsa/skriva testfiler. <br>Tillåtna värden är följande: ”UTF-8”, ”UTF-16”, ”UTF-16BE”, ”UTF-32”, ”UTF-32BE”, ”US-ASCII”, ”UTF-7”, ”BIG5”, ”EUC-JP”, ”EUC-KR”, ”GB2312”, ”GB18030”, ”JOHAB”, ”SHIFT-JIS”, ”CP875”, ”CP866”, ”IBM00858”, ”IBM037”, ”IBM273”, ”IBM437”, ”IBM500”, ”IBM737”, ”IBM775”, ”IBM850” ”, IBM852 ”,” IBM855 ”,” IBM857 ”,” IBM860 ”,” IBM861 ”,” IBM863 ”,” IBM864 ”,” IBM865 ”,” IBM869 ”,” IBM870 ”,” IBM01140 ”,” IBM01141 ”,” IBM01142 ”,” IBM01143 ”,” IBM01144 ”,” IBM01145 ”,” IBM01146 ”,” IBM01147 ”,” IBM01148 ”,” IBM01149 ”,” ISO-2022-JP ”,” ISO-2022-KR ”,” ISO-8859-1 ”,” ISO-8859-2 ”,” ISO-8859-3 ”,” ISO-8859-4 ”,” ISO-8859-5 ”,” ISO-8859-6 ”,” ISO-8859-7 ”,” ISO-8859-8 ”,” ISO-8859-9 ”,” ISO-8859-13 ”,” ISO-8859-15 ”,” WINDOWS-874 ”,” WINDOWS-1250 ”,” WINDOWS-1251 ”,” WINDOWS-1252 ”,” WINDOWS-1253 ””, WINDOWS-1254 ”,” WINDOWS-1255 ”,” WINDOWS-1256 ”,” WINDOWS-1257 ”,” WINDOWS-1258 ”.<br>Observera mappning dataflöde inte stöder UTF-7-kodning. | Nej       |
| compressionCodec | Komprimerings-codec som används för att läsa/skriva textfiler. <br>Tillåtna värden är **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, eller **lz4**. att använda när du sparar filen. <br>Observera för närvarande kopieringsaktiviteten inte stöder ”snappy” och ”lz4” och mappa dataflöde stöder inte ”ZipDeflate”. | Nej       |
| compressionLevel | Komprimeringsförhållandet. <br>Tillåtna värden är **Optimal** eller **snabbast**.<br>- **Snabbaste:** Komprimeringsåtgärden ska slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.<br>- **Optimal**: Komprimeringsåtgärden ska komprimeras optimalt, även om åtgärden tar längre tid att slutföra. Mer information finns i [komprimeringsnivå](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) avsnittet. | Nej       |

Nedan visas ett exempel på en avgränsad text datamängd på Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
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

Följande egenskaper stöds i kopieringsaktiviteten ***\*källa\**** avsnittet.

| Egenskap        | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Type-egenskapen för aktiviteten kopieringskälla måste anges till **DelimitedTextSource**. | Ja      |
| formatSettings | En grupp egenskaper. Referera till **avgränsad text läsinställningar** tabellen nedan. | Nej       |
| storeSettings  | En grupp egenskaper för hur du läser data från ett datalager. Varje filbaserade anslutningsprogrammet har sin egen Läs inställningar som stöds under `storeSettings`. **Mer information finns i artikeln connector -> Kopiera aktivitet egenskapsavsnittet**. | Nej       |

Stöds **avgränsad text läsinställningar** under `formatSettings`:

| Egenskap       | Beskrivning                                                  | Krävs |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vilken typ av formatSettings måste anges till **DelimitedTextReadSetting**. | Ja      |
| skipLineCount | Anger hur många **icke-tomma** rader att hoppa över vid läsning av data från indatafiler. <br>Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. | Nej       |

### <a name="delimited-text-as-sink"></a>Avgränsad text som mottagare

Följande egenskaper stöds i kopieringsaktiviteten ***\*mottagare\**** avsnittet.

| Egenskap        | Beskrivning                                                  | Krävs |
| -------------- | ------------------------------------------------------------ | -------- |
| typ           | Type-egenskapen för aktiviteten kopieringskälla måste anges till **DelimitedTextSink**. | Ja      |
| formatSettings | En grupp egenskaper. Referera till **avgränsad text skriva inställningar** tabellen nedan. |          |
| storeSettings  | En grupp egenskaper om hur du skriver data till ett datalager. Varje filbaserade anslutning har sin egen stöds skrivning inställningar under `storeSettings`. **Mer information finns i artikeln connector -> Kopiera aktivitet egenskapsavsnittet**. | Nej       |

Stöds **avgränsad text skriva inställningar** under `formatSettings`:

| Egenskap       | Beskrivning                                                  | Krävs                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Vilken typ av formatSettings måste anges till **DelimitedTextWriteSetting**. | Ja                                                   |
| fileExtension | Filtillägget som används för att namnge utdatafilerna, t.ex. `.csv`, `.txt`. Det måste vara anges när den `fileName` har inte angetts i utdata DelimitedText datauppsättning. | Ja när filnamnet inte har angetts i datauppsättningen för utdata |

## <a name="mapping-data-flow-properties"></a>Egenskaper för mappning av dataflöde

Få mer detaljerad information från [source omvandling](data-flow-source.md) och [mottagare omvandling](data-flow-sink.md) i mappning dataflöde.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Mappning av dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)