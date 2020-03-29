---
title: Anpassad blob-utdatapartitionering i Azure Stream Analytics
description: I den här artikeln beskrivs de anpassade DateTime-sökvägsmönstren och de anpassade fält- eller attributfunktionerna för blob-lagringsutdata från Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426387"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Anpassad blob-utdatapartitionering i Azure Stream Analytics

Azure Stream Analytics stöder anpassad blob-utdatapartitionering med anpassade fält eller attribut och anpassade DateTime-sökvägsmönster. 

## <a name="custom-field-or-attributes"></a>Anpassat fält eller attribut

Anpassade fält- eller indataattribut förbättrar arbetsflöden för databearbetning och rapportering i nedströms genom att tillåta mer kontroll över utdata.

### <a name="partition-key-options"></a>Alternativ för partitionsnyckel

Partitionsnyckeln, eller kolumnnamnet, som används för att partitionera indata kan innehålla alfanumeriska tecken med bindestreck, understreck och blanksteg. Det går inte att använda kapslade fält som en partitionsnyckel om de inte används tillsammans med alias. Partitionsnyckeln måste vara NVARCHAR(MAX).

### <a name="example"></a>Exempel

Anta att ett jobb tar indata från live-användarsessioner som är anslutna till en extern videospelstjänst där intjänade data innehåller en kolumn **client_id** för att identifiera sessionerna. Om du vill partitionera data **efter client_id**anger du fältet Blob Path Pattern så att det innehåller en partitionstoken **{client_id}** i blob-utdataegenskaper när du skapar ett jobb. När data med olika **client_id** värden flödar genom Stream Analytics-jobbet sparas utdata i separata mappar baserat på ett enda **client_id** värde per mapp.

![Banmönster med klient-ID](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

På samma sätt, om jobbingången var sensordata från miljontals sensorer där varje sensor hade en **sensor_id,** skulle banmönstret vara **{sensor_id}** för att partitionera varje sensordata till olika mappar.  


Med REST API kan utdataavsnittet i en JSON-fil som används för den begäran se ut så här:  

![UTDATA för REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

När jobbet börjar köras kan *klientbehållaren* se ut så här:  

![Behållare för klienter](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Varje mapp kan innehålla flera blobbar där varje blob innehåller en eller flera poster. I exemplet ovan finns det en enda blob i en mapp med etiketten "060000000" med följande innehåll:

![Blob-innehåll](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observera att varje post i blobben har en **client_id** kolumn som matchar mappnamnet sedan kolumnen som används för att partitionera utdata i utdatasökvägen **var client_id**.

### <a name="limitations"></a>Begränsningar

1. Endast en anpassad partitionsnyckel tillåts i blobutmatningsegenskapen Path Pattern. Alla följande banmönster är giltiga:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Partitionsnycklar är skiftlägesokänsliga, så partitionsnycklar som "John" och "john" är likvärdiga. Dessutom kan uttryck inte användas som partitionsnycklar. **{columnA + columnB}** fungerar till exempel inte.  

3. När en indataström består av poster med en partitionsnyckel kardinalitet under 8000, läggs posterna till befintliga blobbar och skapar bara nya blobbar när det behövs. Om kardinaliteten är över 8000 finns det ingen garanti befintliga blobbar kommer att skrivas till och nya blobbar skapas inte för ett godtyckligt antal poster med samma partitionsnyckel.

## <a name="custom-datetime-path-patterns"></a>Anpassade datetime-sökvägsmönster

Med anpassade DateTime-sökvägar kan du ange ett utdataformat som stämmer överens med Hive Streaming-konventioner, vilket ger Azure Stream Analytics möjlighet att skicka data till Azure HDInsight och Azure Databricks för nedströmsbearbetning. Anpassade DateTime-sökvägsmönster implementeras `datetime` enkelt med nyckelordet i fältet Sökvägsprefix för blobutdata, tillsammans med formatspecificeraren. Till exempel `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Token som stöds

Följande formatekta angertoken kan användas ensamt eller i kombination för att uppnå anpassade DateTime-format:

|Formatspecificerare   |Beskrivning   |Resultat på exempeltid 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Året som ett fyrsiffrigt tal|2018|
|{datetime:MM}|Månad från 01 till 12|01|
|{datetime:M}|Månad från 1 till 12|1|
|{datetime:dd}|Dag från 01 till 31|02|
|{datetime:d}|Dag från 1 till 12|2|
|{datetime:HH}|Timme med 24-timmarsformat, från 00 till 23|10|
|{datetime:mm}|Minuter från 00 till 24|06|
|{datetime:m}|Minuter från 0 till 24|6|
|{datetime:ss}|Sekunder från 00 till 60|08|

Om du inte vill använda anpassade DateTime-mönster kan du lägga till {date} och/eller {time}-token i sökvägsprefixet för att generera en listruta med inbyggda DateTime-format.

![Strömma gamla DateTime-format för Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Utökningsbarhet och begränsningar

Du kan använda så `{datetime:<specifier>}`många token, som du vill i banmönstret tills du når teckengränsen för sökvägsprefixet. Formatspecificerare kan inte kombineras inom en enda token utöver de kombinationer som redan anges av datum- och tidslisterutan. 

För en sökvägspartition av: `logs/MM/dd`

|Giltigt uttryck   |Ogiltigt uttryck   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Du kan använda samma formatspecificerare flera gånger i sökvägsprefixet. Token måste upprepas varje gång.

### <a name="hive-streaming-conventions"></a>Hive Streaming konventioner

Anpassade sökvägsmönster för blob-lagring kan användas med Hive Streaming-konventionen, `column=` som förväntar sig att mappar ska märkas med i mappnamnet.

Till exempel `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Anpassad utdata eliminerar besväret med att ändra tabeller och manuellt lägga till partitioner i portdata mellan Azure Stream Analytics och Hive. I stället kan många mappar läggas till automatiskt med hjälp av:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exempel

Skapa ett lagringskonto, en resursgrupp, ett Stream Analytics-jobb och en indatakälla enligt snabbstartsguiden för [Azure Stream Analytics Azure Portal.](stream-analytics-quick-create-portal.md) Använd samma exempeldata som används i snabbstartsguiden, som också finns på [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Skapa en blob-utdatamottagare med följande konfiguration:

![Stream Analytics skapa blob utdatamottagare](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Hela banmönstret är följande:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


När du startar jobbet skapas en mappstruktur baserat på sökvägsmönstret i blob-behållaren. Du kan öka detaljnivån till dagnivå.

![Stream Analytics-blobutdata med anpassat banmönster](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
