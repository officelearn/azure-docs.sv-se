---
title: Azure Stream Analytics partitionering av anpassad BLOB-utdata
description: I den här artikeln beskrivs anpassade patterns Path-mönster och anpassade fält-eller attribut-funktioner för Blob Storage-utdata från Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426387"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics partitionering av anpassad BLOB-utdata

Azure Stream Analytics stöder Anpassad partitionering av BLOB-utdata med anpassade fält eller attribut och anpassade mönster för DateTime-sökvägar. 

## <a name="custom-field-or-attributes"></a>Anpassat fält eller attribut

Anpassade fält eller indatavärden förbättrar underordnade data bearbetnings-och rapporterings arbets flöden genom att tillåta mer kontroll över utdata.

### <a name="partition-key-options"></a>Partitionsalternativ

Partitionsnyckel eller kolumn namn som används för att partitionera indata kan innehålla alfanumeriska tecken med bindestreck, under streck och blank steg. Det går inte att använda kapslade fält som partitionsnyckel om de inte används tillsammans med alias. Partitionsnyckel måste vara NVARCHAR (MAX).

### <a name="example"></a>Exempel

Anta att ett jobb tar indata från Live User-sessioner som är anslutna till en extern video spel tjänst där inmatade data innehåller en kolumn **client_id** för att identifiera sessionerna. Om du vill partitionera data med **client_id**anger du i fältet blobb Sök vägs mönster att innehåller en partitions-token **{client_id}** i egenskaper för BLOB-utdata när du skapar ett jobb. Som data med olika **client_id** värden flödar genom Stream Analytics jobbet, sparas utdata i separata mappar baserat på ett enda **client_id** värde per mapp.

![Sök vägs mönster med klient-ID](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

På samma sätt, om jobb inmatningen var sensor data från miljon tals sensorer där varje sensor hade en **sensor_id**, skulle Sök vägs mönstret vara **{sensor_id}** för att partitionera varje sensor data till olika mappar.  


Med hjälp av REST API kan utmatnings avsnittet i en JSON-fil som används för begäran se ut så här:  

![REST API utdata](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

När jobbet börjar köras kan *klienternas* behållare se ut så här:  

![Behållare för klienter](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Varje mapp kan innehålla flera blobbar där varje BLOB innehåller en eller flera poster. I exemplet ovan finns det en enda BLOB i en mapp med etiketten "06000000" med följande innehåll:

![BLOB-innehåll](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observera att varje post i blobben har en **client_id** kolumn som matchar mappnamnet eftersom kolumnen som användes för att partitionera utdata i sökvägen för utdata var **client_id**.

### <a name="limitations"></a>Begränsningar

1. Det går bara att ha en anpassad partitionsnyckel i sökvägen till BLOB-utdata för Sök vägs mönster. Alla följande Sök vägs mönster är giltiga:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Partitionsnyckel är Skift läges okänsliga, så att partitionstyper som "John" och "John" är likvärdiga. Det går inte heller att använda uttryck som partitionsnyckel. Till exempel fungerar inte **{Columna + columnB}** .  

3. När en indataströmmen består av poster med en partitionsnyckel i 8000, läggs posterna till i befintliga blobbar och skapar bara nya blobbar vid behov. Om kardinalitet är över 8000, finns det ingen garanti för att befintliga blobar ska skrivas till och nya blobbar skapas inte för ett godtyckligt antal poster med samma partitionsnyckel.

## <a name="custom-datetime-path-patterns"></a>Anpassade mönster för DateTime-sökväg

Anpassade DateTime-sökvägsmönster kan du ange utdataformat som överensstämmer med Hive-direktuppspelning konventioner, vilket gör Azure Stream Analytics kan skicka data till Azure HDInsight och Azure Databricks för nedströms bearbetning. Anpassat datum/tid-sökvägsmönster är enkelt implementeras med hjälp av den `datetime` nyckelord i fältet Sökvägsprefix för din blob-utdata, tillsammans med en formatangivelse. Till exempel `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Token som stöds

Följande format specificerare token kan användas fristående eller i kombination för att uppnå anpassade DateTime-format:

|Formatspecifierare   |Beskrivning   |Resultat på exempel tid 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|År som ett fyrsiffrigt tal|2018|
|{datetime:MM}|Månad från 01 till 12|01|
|{datetime:M}|Månad från 1 till 12|1|
|{datetime:dd}|Dag från 01-31|02|
|{datetime:d}|Dag från 1 till 12|2|
|{datetime:HH}|Timme med 24-timmarsformat, från 00 och 23|10|
|{datetime:mm}|Minuter från 00-24|06|
|{datetime:m}|Minuter från 0 till och med 24|6|
|{datetime:ss}|Sekunder från 00 60|08|

Om du inte vill använda anpassade DateTime mönster du kan lägga till {date} och/eller {time} token till Sökvägsprefix att generera en listruta med inbyggda DateTime-format.

![Stream Analytics gamla DateTime-format](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Utökningsbarhet och begränsningar

Du kan använda så många token `{datetime:<specifier>}`, som du precis som i mönstret sökväg tills du når gränsen Sökvägsprefix tecken. Formatet specificerare kan inte kombineras i en enskild token utöver kombinationer som redan visas genom att listrutorna för datum och tid. 

För en sökväg partition av `logs/MM/dd`:

|Giltigt uttryck   |Ogiltigt uttryck   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Du kan använda samma formatspecifierare flera gånger i den sökväg som Prefix. Token ska upprepas varje gång.

### <a name="hive-streaming-conventions"></a>Konventioner för hive-direktuppspelning

Anpassad sökvägsmönster för blob storage kan användas med konventionen Hive-direktuppspelning, som förväntar sig mappar ska förses med `column=` i mappnamnet.

Till exempel `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Anpassade utdata eliminerar behovet av att ändra tabeller och manuellt lägga till partitioner i portdata mellan Azure Stream Analytics och Hive. I stället kan många mappar läggas till automatiskt med:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exempel

Skapa ett lagringskonto, en resursgrupp, ett Stream Analytics-jobb och en indatakälla enligt den [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) snabbstartsguiden. Använda samma exempeldata som används i snabbstartsguiden även tillgänglig på [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Skapa en blob-utdatamottagare med följande konfiguration:

![Skapa blob utdatamottagare för Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Fullständig sökväg mönstret är följande:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


När du startar jobbet, skapas en mappstruktur baserat på sökvägsmönster i blob-behållare. Du kan öka detaljnivån till nivån dag.

![Stream Analytics blob-utdata med anpassade sökvägar](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
