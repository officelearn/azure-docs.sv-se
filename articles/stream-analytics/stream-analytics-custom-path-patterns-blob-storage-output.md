---
title: Azure Stream Analytics anpassade blob-utdata partitionering
description: Den här artikeln beskriver de anpassade DateTime-sökvägsmönster och anpassade fält eller attribut funktioner för blob storage-utdata från Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771861"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics anpassade blob-utdata partitionering

Azure Stream Analytics stöder anpassade blob-utdata partitionering med anpassade fält eller attribut och anpassade DateTime sökvägsmönster. 

## <a name="custom-field-or-attributes"></a>Anpassat fält eller attribut

Anpassat fält eller indataattribut förbättra nedströms databearbetning och rapportering arbetsflöden genom att tillåta mer kontroll över utdata.

### <a name="partition-key-options"></a>Alternativen för partition

Partitionsnyckel eller kolumnnamn som används för att partitionera indata får innehålla alfanumeriska tecken med bindestreck, understreck och blanksteg. Det går inte att använda kapslade fält som en partitionsnyckel såvida används tillsammans med alias.

### <a name="example"></a>Exempel

Anta att ett jobb tar indata från live användarsessioner som är anslutna till en extern videospel-tjänst där insamlade data innehåller en kolumn **client_id** att identifiera sessioner. Att partitionera data genom att **client_id**, ange fältet Blob Sökvägsmönster att inkludera en partition token **{client_id}** i Egenskaper för blob-utdata när du skapar ett jobb. Som data med olika **client_id** värden flödar genom ett Stream Analytics-jobb, utdata sparas i separata mappar baserat på en enda **client_id** värde per mapp.

![Sökvägsmönster med klient-id](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

På samma sätt, om jobbet indata har sensordata från miljontals sensorer där varje sensor hade en **sensor_id**, mönstret sökvägen skulle vara **{sensor_id}** att partitionera varje sensordata till olika mappar.  


Med hjälp av REST-API, utdataavsnittet i en JSON kan-fil som används för denna förfrågan se ut så här:  

![REST API-utdata](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

När jobbet börjar köras, det *klienter* behållare kan se ut så här:  

![Behållare för klienter](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Varje mapp kan innehålla flera blobar där varje blob innehåller en eller flera poster. I exemplet ovan finns en enda blob i en mapp med namnet ”06000000” med följande innehåll:

![Blobbinnehåll](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observera att varje post i blob har en **client_id** matchar mappen kolumnnamnet eftersom kolumnen används för att partitionera utdata på Utdatasökvägen **client_id**.

### <a name="limitations"></a>Begränsningar

1. Endast en anpassad partitionsnyckel tillåts i egenskapen Sökvägsmönster blob-utdata. Alla följande Sökvägsmönster är giltiga:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. Partitionsnycklar är skiftlägeskänsligt, så partitionsnycklar som ”John” och ”john” är likvärdiga. Uttryck kan inte användas som partitionsnycklar. Till exempel **{columnA + columnB}** fungerar inte.  

3. När en indataström som består av poster med en partition viktiga kardinalitet under 8000, poster läggs till befintliga blobar och bara skapa nya blobbar när det behövs. Om Kardinaliteten är över 8000 som det finns ingen garanti för befintliga blobbar ska skrivas till och nya blobbar skapas inte för ett valfritt antal poster med samma partitionsnyckel.  

## <a name="custom-datetime-path-patterns"></a>Anpassat datum/tid-sökvägsmönster

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
