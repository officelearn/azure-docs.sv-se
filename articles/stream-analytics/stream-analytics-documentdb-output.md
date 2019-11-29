---
title: Azure Stream Analytics utdata till Cosmos DB
description: Den här artikeln beskriver hur du använder Azure Stream Analytics för att spara utdata till Azure Cosmos DB för JSON-utdata, för dataarkivering och frågor med låg latens i ostrukturerade JSON-data.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560188"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics utdata till Azure Cosmos DB  
Stream Analytics kan rikta [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) för JSON-utdata, aktivera dataarkivering och frågor med låg latens i OSTRUKTURERAde JSON-data. Det här dokumentet beskriver några metod tips för att implementera den här konfigurationen.

För dem som inte är bekant med Cosmos DB kan du ta en titt på [Azure Cosmos DB utbildnings väg](https://azure.microsoft.com/documentation/learning-paths/documentdb/) för att komma igång. 

> [!Note]
> För tillfället stöder Azure Stream Analytics endast anslutning till Azure Cosmos DB med **SQL API**.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du pekar Azure Stream Analytics till de Azure Cosmos DB konton som skapats med andra API: er, kanske data inte lagras korrekt. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Grunderna för Cosmos DB som ett utgående mål
Azure Cosmos DB utdata i Stream Analytics gör det möjligt att skriva data ström bearbetnings resultatet som JSON-utdata till dina Cosmos DB-behållare. Stream Analytics skapar inte behållare i databasen, i stället måste du skapa dem direkt. Detta är så att fakturerings kostnaderna för Cosmos DB behållare styrs av dig, och så att du kan justera prestanda, konsekvens och kapacitet för dina behållare direkt med hjälp av Cosmos DB- [API: er](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Du måste lägga till 0.0.0.0 i listan över tillåtna IP-adresser från din Azure Cosmos DB-brandvägg.

Några av alternativen för Cosmos DB behållare beskrivs nedan.

## <a name="tune-consistency-availability-and-latency"></a>Justera konsekvens, tillgänglighet och svars tid
För att matcha dina program krav kan du Azure Cosmos DB finjustera databasen och behållarna och göra kompromisser mellan konsekvens, tillgänglighet, svars tid och data flöde. Beroende på vilka nivåer av Läs konsekvens som ditt scenario behöver för Läs-och skriv fördröjning kan du välja en konsekvens nivå för ditt databas konto. Data flödet kan förbättras genom att skala upp enheter för programbegäran (ru: er) på behållaren. Som standard aktiverar Azure Cosmos DB synkron indexering på varje CRUD-åtgärd till din behållare. Detta är ett annat användbart alternativ för att styra Skriv-/Läs prestanda i Azure Cosmos DB. Mer information finns i artikeln [ändra din databas och fråga konsekvens nivåer](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsertar från Stream Analytics
Med Stream Analytics integration med Azure Cosmos DB kan du infoga eller uppdatera poster i din behållare baserat på en specifik dokument-ID-kolumn. Detta kallas även för en *upsert*.

Stream Analytics använder en optimistisk upsert-metod där uppdateringar bara utförs när INSERT Miss lyckas med en dokument-ID-konflikt. Med kompatibilitetsnivån 1,0 utförs den här uppdateringen som en korrigering, så den aktiverar partiella uppdateringar av dokumentet, det vill säga att tillägg av nya egenskaper eller att ersätta en befintlig egenskap görs stegvis. Men ändringar i värdena för mat ris egenskaper i JSON-dokumentet resulterar i att hela matrisen kommer att bli överskriven, det vill säga att matrisen inte sammanfogas. Med 1,2 ändras upsert-beteendet för att infoga eller ersätta dokumentet. Detta beskrivs närmare i avsnittet Compatibility nivå 1,2 nedan.

Om det inkommande JSON-dokumentet har ett befintligt ID-fält används automatiskt det fältet som dokument-ID-kolumn i Cosmos DB och eventuella efterföljande skrivningar hanteras som sådana, vilket leder till någon av följande situationer:
- unikt ID som leder till infogning
- dubblett-ID: n och "dokument-ID" har angetts till "ID" leder till upsert
- dubblett-ID: n och dokument-ID: t angavs inte leads till fel, efter det första dokumentet

Om du vill spara <i>alla</i> dokument som innehåller ett duplicerat ID byter du namn på fältet ID i frågan (med nyckelordet som nyckelord) och låter Cosmos DB skapa fältet ID eller ersätta ID: t med en annan kolumns värde (med hjälp av nyckelordet "dokument-ID").

## <a name="data-partitioning-in-cosmos-db"></a>Data partitionering i Cosmos DB
Azure Cosmos DB [obegränsade](../cosmos-db/partition-data.md) behållare är den metod som rekommenderas för att partitionera dina data, eftersom Azure Cosmos DB automatiskt skalar partitioner utifrån din arbets belastning. När du skriver till obegränsade behållare, Stream Analytics använda så många parallella skrivare som föregående fråge steg eller schema för inmatnings partitionering.
> [!NOTE]
> För tillfället stöder Azure Stream Analytics endast obegränsade behållare med partitionsnyckel på den högsta nivån. `/region` stöds till exempel. Kapslade partitionsnyckel (t. ex. `/region/name`) stöds inte. 

Beroende på ditt val av partitionsnyckel kan du få den här _varningen_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Det är viktigt att välja en partitionsnyckel som har ett antal distinkta värden och gör att du kan distribuera din arbets belastning jämnt över dessa värden. Som en naturlig artefakt för partitionering begränsas begär Anden som involverar samma partitionsnyckel av det maximala data flödet för en enda partition. Dessutom är lagrings storleken för dokument som tillhör samma partitionsnyckel begränsad till 10 GB. En idealisk partitionsnyckel är en som ofta visas som ett filter i dina frågor och har tillräckligt med kardinalitet för att säkerställa att din lösning är skalbar.

En partitionsnyckel är också en avgränsning för transaktioner i DocumentDB lagrade procedurer och utlösare. Du bör välja partitionsnyckel så att dokument som förekommer tillsammans i transaktioner delar samma partitionsnyckel. Artikeln [partitionering i Cosmos DB](../cosmos-db/partitioning-overview.md) ger mer information om hur du väljer en partitionsnyckel.

För fasta Azure Cosmos DB behållare kan Stream Analytics inte skala upp eller ut när de är fulla. De har en övre gräns på 10 GB och 10 000 RU/s-genomflöde.  Om du vill migrera data från en fast behållare till en obegränsad behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel) måste du använda [verktyget datamigrering](../cosmos-db/import-data.md) eller [ändra feed-biblioteket](../cosmos-db/change-feed.md).

Möjligheten att skriva till flera fasta behållare är inaktuell och rekommenderas inte för att skala ut ditt Stream Analytics jobb.

## <a name="improved-throughput-with-compatibility-level-12"></a>Förbättrat data flöde med kompatibilitetsnivå 1,2
Med kompatibilitetsnivå 1,2 stöder Stream Analytics inbyggd integrering för Mass skrivning till Cosmos DB. Detta gör det möjligt att skriva effektivt för att Cosmos DB med maximera genom strömning och effektiv hantering av begränsnings begär Anden. Den förbättrade Skriv mekanismen är tillgänglig under en ny kompatibilitetsnivå på grund av en upsert beteende skillnad.  Före 1,2 är beteendet för upsert att infoga eller sammanfoga dokumentet. Med 1,2 ändras upsertar-beteendet för att infoga eller ersätta dokumentet.

Före 1,2 använder en anpassad lagrad procedur för att massredigera upsert-dokument per partitionsnyckel till Cosmos DB, där en batch skrivs som en transaktion. Även om en enskild post träffar ett tillfälligt fel (begränsning), måste hela batchen göras om. Detta gör scenarier med ännu rimlig begränsning relativt långsammare. Följande jämförelse visar hur sådana jobb beter sig med 1,2.

I följande exempel visas två identiska Stream Analytics jobb som läser från samma Event Hub-indatatyper. Både Stream Analytics jobb är [helt partitionerade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) med en passthrough-fråga och skriver till identiska CosmosDB-behållare. Mått till vänster är från det jobb som kon figurer ATS med kompatibilitetsnivå 1,0 och de till höger konfigureras med 1,2. En Cosmos DB behållares partitionsnyckel är ett unikt GUID som kommer från indatamängden.

![jämförelse av Stream Analytics-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Inkommande händelse frekvens i Event Hub är 2x högre än Cosmos DB behållare (20 000 ru: er) har kon figurer ATS för insugning, vilket innebär att begränsningen förväntas i Cosmos DB. Jobbet med 1,2 skriver dock konsekvent med ett högre data flöde (utdata-händelser/minut) och med en lägre genomsnittlig SU%-användning. I din miljö är skillnaden beroende av några fler faktorer, till exempel val av händelse format, händelse-/meddelande storlek, partitionstyper, frågor osv.

![jämförelse av Cosmos DB-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Med 1,2 är Stream Analytics mer intelligent med att använda 100% av det tillgängliga genomflödet i Cosmos DB med mycket få återsändningar från begränsning/hastighets begränsning. Detta ger en bättre upplevelse för andra arbets belastningar, t. ex. frågor som körs på behållaren på samma gång. Om du behöver testa hur ASA skalas ut med Cosmos DB som mottagare för 1 KB till 10 000 meddelanden/sekund, här är ett Azure- [exempel projekt](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) som gör det möjligt att göra det.
Observera att Cosmos DB data flöde är identiskt med 1,0 och 1,1. Eftersom 1,2 för närvarande inte är standard, kan du [Ange kompatibilitetsnivå](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) för ett Stream Analytics jobb med hjälp av portalen eller genom att använda [anropet skapa jobb REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Vi *rekommenderar starkt* att du använder kompatibilitetsnivån 1,2 i ASA med Cosmos dB.



## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB inställningar för JSON-utdata

Om du skapar Cosmos DB som utdata i Stream Analytics genereras en prompt för information som visas nedan. Det här avsnittet innehåller en förklaring av egenskaps definitionen.

![skärm bild för DocumentDB Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Fält           | Beskrivning|
|-------------   | -------------|
|Utdataalias    | Ett alias för att referera till utdata i din ASA-fråga.|
|Prenumeration    | Välj Azure-prenumerationen.|
|Konto-ID      | Azure Cosmos DB kontots namn eller slut punkts-URI.|
|Konto nyckel     | Den delade åtkomst nyckeln för det Azure Cosmos DB kontot.|
|Databas        | Namnet på Azure Cosmos DBs databasen.|
|Containerns namn | Namnet på den behållare som ska användas. `MyContainer` är ett exempel på en giltig indatamängd-en behållare med namnet `MyContainer` måste finnas.  |
|Dokument-ID     | Valfri. Kolumn namnet i utmatnings händelser som används som den unika nyckel som infognings-eller uppdaterings åtgärder måste baseras på. Om det lämnas tomt infogas alla händelser, utan uppdaterings alternativ.|

När Cosmos DB utdata har kon figurer ATS kan den användas i frågan som mål för en [into-instruktion](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). När du använder en Cosmos DB utdata som sådan [måste en partitionsnyckel anges explicit](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). Output-posten måste innehålla en Skift läges känslig kolumn med namnet efter partitionsnyckel i Cosmos DB. För att uppnå större parallellisering kan instruktionen kräva en [partition by-sats](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) med samma kolumn.

**Exempel fråga**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Fel hantering och återförsök

I händelse av ett tillfälligt fel, tjänsten är inte tillgänglig eller begränsning vid sändning av händelser till Cosmos DB, Stream Analytics nya försök för att slutföra åtgärden. Det finns dock vissa fel för vilka återförsök inte sker, däribland följande:

- Obehörig (http-Felkod 401)
- NotFound (http-Felkod 404)
- Tillåts inte (http-felkod 403)
- BadRequest (http-felkod 400)
