---
title: Dataformat som stöds av Azure Data Explorer för inmatning.
description: Lär dig mer om de olika data- och komprimeringsformat som stöds av Azure Data Explorer för inmatning.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235304"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Dataformat som stöds av Azure Data Explorer för inmatning

Datainmatning är den process genom vilken data läggs till i en tabell och görs tillgängliga för frågor i Azure Data Explorer. För alla inmatningsmetoder, förutom intag från fråga, måste data vara i något av de format som stöds. I följande tabell visas och beskrivs de format som Azure Data Explorer stöder för datainmatning.

|Format   |Anknytning   |Beskrivning|
|---------|------------|-----------|
|Avro     |`.avro`     |En [Avro-behållarfil](https://avro.apache.org/docs/current/). Följande koder `null`stöds: `deflate` `snappy` , ( stöds för närvarande inte).|
|CSV      |`.csv`      |En textfil med kommaavgränsade värden (`,`). Se [RFC 4180: _Common Format och MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |En textfil med JSON-objekt `\n` avgränsade av eller `\r\n`. Se [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON (på annat sätt)|`.multijson`|En textfil med en JSON-matris med egenskapspåsar (var och en som representerar `\n` `\r\n`en post) eller valfritt antal egenskapspåsar avgränsade efter blanktecken, eller . Varje egenskapsväska kan spridas på flera rader. Det här formatet `JSON`är att föredra framför , såvida inte data är icke-egendom påsar.|
|Orc      |`.orc`      |En [Orc-fil](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parkett  |`.parquet`  |En [parkett fil](https://en.wikipedia.org/wiki/Apache_Parquet).|
|Psv      |`.psv`      |En textfil med röravgränsade värden (<code>&#124;</code>).|
|Raw      |`.raw`      |En textfil vars hela innehåll är ett enda strängvärde.|
|SCsv (SCsv)     |`.scsv`     |En textfil med semikolonavgränsade värden (`;`).|
|SOHsv (på)    |`.sohsv`    |En textfil med SOH-avgränsade värden. (SOH är ASCII-kodpunkt 1; det här formatet används av Hive på HDInsight.)|
|Tsv      |`.tsv`      |En textfil med tabbavgränsade värden (`\t`).|
|TSVE (av )     |`.tsv`      |En textfil med tabbavgränsade värden (`\t`). Ett omvänt snedstreck (`\`) används för att fly.|
|TXT      |`.txt`      |En textfil med rader avgränsade av `\n`. Tomma rader hoppas över.|

## <a name="supported-data-compression-formats"></a>Datakomprimeringsformat som stöds

Blobbar och filer kan komprimeras genom någon av följande komprimeringsalgoritmer:

|Komprimering|Anknytning|
|-----------|---------|
|Gzip       |.gz      |
|Zip        |.zip     |

Ange komprimering genom att lägga till tillägget till namnet på blobben eller filen.

Ett exempel:
* `MyData.csv.zip`anger en blob eller en fil formaterad som CSV, komprimerad med ZIP (arkiv eller en enda fil)
* `MyData.csv.gz`anger en blob eller en fil formaterad som CSV, komprimerad med GZip

Blob- eller filnamn som inte innehåller formattilläggen men bara komprimering (till exempel) stöds också. I det här fallet måste filformatet anges som en inmatningsegenskap eftersom det inte går att sluta sig till.

> [!NOTE]
> Vissa komprimeringsformat håller reda på det ursprungliga filtillägget som en del av den komprimerade strömmen. Det här tillägget ignoreras vanligtvis för att bestämma filformatet. Om filformatet inte kan bestämmas från blobben (komprimerad) eller filnamnet `format` måste det anges via egenskapen inmatning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [datainmatning](/azure/data-explorer/ingest-data-overview)
* Läs mer om [egenskaper för datainmatning av Azure Data Explorer](ingestion-properties.md)
