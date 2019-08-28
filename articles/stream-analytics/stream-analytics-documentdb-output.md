---
title: Azure Stream Analytics-utdata till Cosmos DB
description: Den här artikeln beskriver hur du använder Azure Stream Analytics för att spara utdata till Azure Cosmos DB för JSON-utdata för dataarkivering och frågor med låg latens för Ostrukturerade JSON-data.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062010"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-utdata till Azure Cosmos DB  
Stream Analytics kan riktas mot [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) att aktivera arkivering och låg latens datafrågor för Ostrukturerade JSON-data för JSON-utdata. Det här dokumentet beskriver några av metodtipsen för att implementera den här konfigurationen.

För de som inte är bekant med Cosmos DB, ta en titt på [Utbildningsväg för Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) att komma igång. 

> [!Note]
> Just nu, Azure Stream Analytics endast stöd för anslutning till Azure Cosmos DB med hjälp av **SQL API**.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du punkt Azure Stream Analytics till Azure Cosmos DB-konton som skapats med andra API: er, kanske data inte korrekt lagras. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Grunderna i Cosmos DB som en utdatamål
Azure Cosmos DB utdata i Stream Analytics gör det möjligt att skriva data ström bearbetnings resultatet som JSON-utdata till dina Cosmos DB-behållare. Stream Analytics skapar inte behållare i databasen, i stället måste du skapa dem direkt. Detta är så att fakturerings kostnaderna för Cosmos DB behållare styrs av dig, och så att du kan justera prestanda, konsekvens och kapacitet för dina behållare direkt med hjälp av Cosmos DB- [API: er](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Du måste lägga till 0.0.0.0 i listan över tillåtna IP-adresser från din Azure Cosmos DB-brandvägg.

Några av alternativen för Cosmos DB behållare beskrivs nedan.

## <a name="tune-consistency-availability-and-latency"></a>Finjustera konsekvens, tillgänglighet och svarstid
För att matcha dina program krav kan du Azure Cosmos DB finjustera databasen och behållarna och göra kompromisser mellan konsekvens, tillgänglighet, svars tid och data flöde. Beroende på vilka nivåer av läsningskontinuitet behoven scenariot mot läser och skriver svarstid, du kan välja en konsekvensnivå på ditt databaskonto. Data flödet kan förbättras genom att skala upp enheter för programbegäran (ru: er) på behållaren. Som standard aktiverar Azure Cosmos DB synkron indexering på varje CRUD-åtgärd till din behållare. Det här är ett annat användbart alternativ för att styra skrivning/läsning prestanda i Azure Cosmos DB. Mer information finns i [ändra din databas och fråga konsekvensnivåer](../cosmos-db/consistency-levels.md) artikeln.

## <a name="upserts-from-stream-analytics"></a>Upsertar från Stream Analytics
Med Stream Analytics integration med Azure Cosmos DB kan du infoga eller uppdatera poster i din behållare baserat på en specifik dokument-ID-kolumn. Detta är kallas även en *Upsert*.

Stream Analytics använder en optimistisk upsert-metod, där uppdateringar utförs endast när insert misslyckas med ett dokument-ID-konflikt. Med kompatibilitetsnivån 1,0 utförs den här uppdateringen som en korrigering, så den aktiverar partiella uppdateringar av dokumentet, det vill säga att tillägg av nya egenskaper eller att ersätta en befintlig egenskap görs stegvis. Ändringar i värdena för matris egenskaper i JSON-dokument-resultat i hela matrisen komma över, det vill säga matrisen inte slagit samman. Med 1,2 ändras upsert-beteendet för att infoga eller ersätta dokumentet. Detta beskrivs närmare i avsnittet Compatibility nivå 1,2 nedan.

Om inkommande JSON-dokumentet har ett befintligt ID-fält att fältet används automatiskt som dokument-ID-kolumn i Cosmos DB och alla efterföljande skrivningar hanteras därför leder till någon av dessa situationer:
- unika ID: N leda till att infoga
- dubbla ID: N och ”dokument-ID” inställd på ”ID” leder till upsert
- dubbla ID: N och dokument-ID inte set leder till fel, efter det första dokumentet

Om du vill spara <i>alla</i> dokument, inklusive som med en dubblett-ID, byta namn på ID-fältet i din fråga (med nyckelordet AS) och låt Cosmos DB skapa ID-fältet eller ersätta ID med en annan kolumn värde (med hjälp av nyckelordet AS eller med inställningen dokument-ID).

## <a name="data-partitioning-in-cosmos-db"></a>Datapartitionering i Cosmos DB
Azure Cosmos DB [obegränsade](../cosmos-db/partition-data.md) behållare är den metod som rekommenderas för att partitionera dina data, eftersom Azure Cosmos DB automatiskt skalar partitioner utifrån din arbets belastning. När du skriver till obegränsade behållare, Stream Analytics använda så många parallella skrivare som föregående fråge steg eller schema för inmatnings partitionering.
> [!NOTE]
> För tillfället stöder Azure Stream Analytics endast obegränsade behållare med partitionsnyckel på den högsta nivån. Till exempel `/region` stöds. Kapslade partitionsnycklar (t.ex. `/region/name`) stöds inte. 

Beroende på ditt val av partitionsnyckel kan du få den här varningen:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Det är viktigt att välja en partitionsnyckel som har ett antal distinkta värden och gör att du kan distribuera din arbets belastning jämnt över dessa värden. Som en naturlig artefakt för partitionering begränsas begär Anden som involverar samma partitionsnyckel av det maximala data flödet för en enda partition. Dessutom är lagrings storleken för dokument som tillhör samma partitionsnyckel begränsad till 10 GB. En idealisk partitionsnyckel är en som ofta visas som ett filter i dina frågor och har tillräckligt med kardinalitet för att säkerställa att din lösning är skalbar.

En partitionsnyckel är också en avgränsning för transaktioner i DocumentDB lagrade procedurer och utlösare. Du bör välja partitionsnyckel så att dokument som förekommer tillsammans i transaktioner delar samma partitionsnyckel. Artikeln [partitionering i Cosmos DB](../cosmos-db/partitioning-overview.md) ger mer information om hur du väljer en partitionsnyckel.

För fasta Azure Cosmos DB behållare kan Stream Analytics inte skala upp eller ut när de är fulla. De har en övre gräns på 10 GB och 10 000 RU/s genomströmning.  Om du vill migrera data från en fast behållare till en obegränsad behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel), måste du använda den [datamigreringsverktyget](../cosmos-db/import-data.md) eller [ändringsfeed biblioteket](../cosmos-db/change-feed.md).

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



## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB-inställningar för JSON-utdata

Skapar Cosmos DB som utdata i Stream Analytics genererar en uppmaning information som visas nedan. Det här avsnittet innehåller en förklaring av egenskaper-definition.

![documentdb stream analytics utdata skärmen](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Fält           | Beskrivning|
|-------------   | -------------|
|Utdataalias    | Ett alias för att referera till utdata i din ASA-fråga.|
|Subscription    | Välj Azure-prenumerationen.|
|Konto-ID      | Azure Cosmos DB kontots namn eller slut punkts-URI.|
|Kontonyckel     | Den delade åtkomst nyckeln för det Azure Cosmos DB kontot.|
|Databas        | Namnet på Azure Cosmos DBs databasen.|
|Behållarnamn | Namnet på den behållare som ska användas. `MyContainer`är ett exempel på en giltig indatamängd- `MyContainer` en behållare med namnet måste finnas.  |
|Dokument-id     | Valfri. Kolumnnamnet i utdatahändelserna används som den unika nyckeln åtgärder måste baseras på vilken insert eller update. Om fältet lämnas tomt kommer alla händelser att infogas, där du inte uppdateringen.|

## <a name="error-handling-and-retries"></a>Fel hantering och återförsök

I händelse av ett tillfälligt fel, tjänsten är inte tillgänglig eller begränsning vid sändning av händelser till Cosmos DB, Stream Analytics nya försök för att slutföra åtgärden. Det finns dock vissa fel för vilka återförsök inte sker, däribland följande:

- Obehörig (http-Felkod 401)
- NotFound (http-Felkod 404)
- Tillåts inte (http-felkod 403)
- BadRequest (http-felkod 400)
