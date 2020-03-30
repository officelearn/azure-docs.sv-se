---
title: Azure Stream Analytics-utdata till Azure Cosmos DB
description: I den här artikeln beskrivs hur du använder Azure Stream Analytics för att spara utdata till Azure Cosmos DB för JSON-utdata, för dataarkivering och frågor med låg latens på ostrukturerade JSON-data.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254448"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-utdata till Azure Cosmos DB  
Azure Stream Analytics kan rikta in sig på [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) för JSON-utdata, vilket möjliggör dataarkivering och frågor med låg latens på ostrukturerade JSON-data. Det här dokumentet innehåller några metodtips för att implementera den här konfigurationen.

Om du inte är bekant med Azure Cosmos DB läser du [Azure Cosmos DB-dokumentationen](https://docs.microsoft.com/azure/cosmos-db/) för att komma igång. 

> [!Note]
> För närvarande stöder Stream Analytics endast anslutning till Azure Cosmos DB via *SQL API*.
> Andra Azure Cosmos DB API:er stöds ännu inte. Om du pekar Stream Analytics till Azure Cosmos DB-konton som skapats med andra API:er kanske data inte lagras korrekt. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Grunderna i Azure Cosmos DB som utdatamål
Azure Cosmos DB-utdata i Stream Analytics gör det möjligt att skriva dina dataströmbearbetningsresultat som JSON-utdata i dina Azure Cosmos DB-behållare. 

Stream Analytics skapar inte behållare i databasen. Istället kräver det att du skapar dem på framsidan. Du kan sedan styra faktureringskostnaderna för Azure Cosmos DB-behållare. Du kan också justera prestanda, konsekvens och kapacitet för dina behållare direkt med hjälp av [Azure Cosmos DB API:er](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Du måste lägga till 0.0.0.0 i listan över tillåtna IPs från din Azure Cosmos DB-brandvägg.

I följande avsnitt beskrivs några av behållaralternativen för Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Justera konsekvens, tillgänglighet och svarstid
För att matcha dina programkrav kan du med Azure Cosmos DB finjustera databasen och behållarna och göra kompromisser mellan konsekvens, tillgänglighet, svarstid och dataflöde. 

Beroende på vilka nivåer av läskonsekvens ditt scenario behöver mot läs- och skrivfördröjning kan du välja en konsekvensnivå i databaskontot. Du kan förbättra dataflödet genom att skala upp begärandeenheter (RU: er) på behållaren. 

Som standard aktiverar Azure Cosmos DB också synkron indexering på varje CRUD-åtgärd till din behållare. Detta är ett annat användbart alternativ för att styra skriv-/läsprestanda i Azure Cosmos DB. 

Mer information finns i artikeln [Ändra databas- och frågekonsekvensnivåer.](../cosmos-db/consistency-levels.md)

## <a name="upserts-from-stream-analytics"></a>Upserts från Stream Analytics
Stream Analytics-integrering med Azure Cosmos DB gör att du kan infoga eller uppdatera poster i din behållare baserat på en viss **dokument-ID-kolumn.** Detta kallas också ett *upsert*.

Stream Analytics använder en optimistisk upsert-metod. Uppdateringar sker bara när en infogning misslyckas med en dokument-ID-konflikt. 

Med kompatibilitetsnivå 1.0 utför Stream Analytics den här uppdateringen som en PATCH-åtgärd, så den aktiverar partiella uppdateringar av dokumentet. Stream Analytics lägger till nya egenskaper eller ersätter en befintlig egenskap stegvis. Ändringar i värdena för matrisegenskaper i JSON-dokumentet resulterar dock i att hela matrisen skrivs över. Det vill än, matrisen är inte sammanslagen. 

Med 1.2 ändras upsert-beteende för att infoga eller ersätta dokumentet. I det senare avsnittet om kompatibilitetsnivå 1.2 beskrivs detta beteende ytterligare.

Om det inkommande JSON-dokumentet har ett befintligt ID-fält används det fältet automatiskt som **dokument-ID-kolumn** i Azure Cosmos DB. Alla efterföljande skrivningar hanteras som sådana, vilket leder till en av dessa situationer:

- Unika ID:er leder till att infogas.
- Dubblett-ID:er och **dokument-ID** som är inställda på **ID** leder till upsert.
- Dubblett-ID:n och **dokument-ID** har inte angetts efter det första dokumentet.

Om du vill spara *alla* dokument, inklusive de som har ett dubblett-ID, byter du namn på ID-fältet i frågan (med nyckelordet **AS).** Låt Azure Cosmos DB skapa ID-fältet eller ersätta ID:t med en annan kolumns värde (med nyckelordet **AS** eller med hjälp av inställningen **Dokument-ID).**

## <a name="data-partitioning-in-azure-cosmos-db"></a>Datapartitionering i Azure Cosmos DB
Azure Cosmos DB skalar automatiskt partitioner baserat på din arbetsbelastning. Så vi rekommenderar [obegränsade](../cosmos-db/partition-data.md) behållare som metod för partitionering av dina data. När Stream Analytics skriver till obegränsade behållare används lika många parallella författare som föregående frågesteg eller indatapartitioneringsschema.

> [!NOTE]
> Azure Stream Analytics stöder endast obegränsade behållare med partitionsnycklar på den översta nivån. Till exempel `/region` stöds. Kapslade partitionsnycklar `/region/name`(till exempel) stöds inte. 

Beroende på vilket val av partitionsnyckel du väljer kan du få den här _varningen:_

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Det är viktigt att välja en partitionsnyckelegenskap som har ett antal olika värden och som gör att du kan fördela din arbetsbelastning jämnt över dessa värden. Som en naturlig artefakt för partitionering begränsas begäranden som involverar samma partitionsnyckel av det maximala dataflödet för en enskild partition. 

Lagringsstorleken för dokument som tillhör samma partitionsnyckel är begränsad till 10 GB. En idealisk partitionsnyckel är en som ofta visas som ett filter i dina frågor och har tillräcklig kardinalitet för att säkerställa att din lösning är skalbar.

En partitionsnyckel är också gränsen för transaktioner i lagrade procedurer och utlösare för Azure Cosmos DB. Du bör välja partitionsnyckeln så att dokument som förekommer tillsammans i transaktioner delar samma partitionsnyckelvärde. Artikeln [Partitionering i Azure Cosmos DB](../cosmos-db/partitioning-overview.md) ger mer information om hur du väljer en partitionsnyckel.

För fasta Azure Cosmos DB-behållare tillåter Stream Analytics inget sätt att skala upp eller ut när de är fulla. De har en övre gräns på 10 GB och 10 000 RU/s genomströmning. Om du vill migrera data från en fast behållare till en obegränsad behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel) använder du [verktyget för datamigrering](../cosmos-db/import-data.md) eller [ändringsmatningsbiblioteket](../cosmos-db/change-feed.md).

Möjligheten att skriva till flera fasta behållare håller på att inaktuellt. Vi rekommenderar det inte för att skala ut ditt Stream Analytics-jobb.

## <a name="improved-throughput-with-compatibility-level-12"></a>Förbättrat dataflöde med kompatibilitetsnivå 1.2
Med kompatibilitetsnivå 1.2 stöder Stream Analytics inbyggd integrering för massskrivning i Azure Cosmos DB. Den här integreringen gör det möjligt att skriva effektivt till Azure Cosmos DB samtidigt som du maximerar dataflödet och effektivt hanterar begränsningsbegäranden. 

Den förbättrade skrivmekanismen är tillgänglig under en ny kompatibilitetsnivå på grund av en skillnad i upsert beteende. Med nivåer före 1.2 är det öppna beteendet att infoga eller sammanfoga dokumentet. Med 1.2 ändras upsert-beteende för att infoga eller ersätta dokumentet.

Med nivåer före 1.2 använder Stream Analytics en anpassad lagrad procedur för att massera upsert-dokument per partitionsnyckel till Azure Cosmos DB. Där skrivs en batch som en transaktion. Även när en enskild post träffar ett tillfälligt fel (begränsning) måste hela batchen göras om. Detta gör scenarier med även rimlig begränsning relativt långsam.

I följande exempel visas två identiska Stream Analytics-jobb som läser från samma Azure Event Hubs-indata. Båda Stream Analytics-jobben [är helt partitionerade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) med en genomströmningsfråga och skriv till identiska Azure Cosmos DB-behållare. Mått till vänster kommer från jobbet som konfigurerats med kompatibilitetsnivå 1.0. Mått till höger är konfigurerade med 1.2. En Azure Cosmos DB-behållares partitionsnyckel är ett unikt GUID som kommer från indatahändelsen.

![Jämförelse av Stream Analytics-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Den inkommande händelsefrekvensen i Event Hubs är två gånger högre än Azure Cosmos DB-behållare (20 000 RU: er) är konfigurerade för att ta in, så begränsning förväntas i Azure Cosmos DB. Jobbet med 1,2 skrivs dock konsekvent vid ett högre dataflöde (utdatahändelser per minut) och med ett lägre genomsnittligt SU%-utnyttjande. I din miljö beror denna skillnad på några fler faktorer. Dessa faktorer inkluderar val av händelseformat, indatahändelse/meddelandestorlek, partitionsnycklar och fråga.

![Jämförelse av Azure Cosmos DB-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Med 1.2 är Stream Analytics mer intelligent när det gäller att använda 100 procent av det tillgängliga dataflödet i Azure Cosmos DB med mycket få nya inlämningar från begränsning eller hastighetsbegränsning. Detta ger en bättre upplevelse för andra arbetsbelastningar som frågor som körs på behållaren samtidigt. Om du vill se hur Stream Analytics skalas ut med Azure Cosmos DB som en diskho för 1 000 till 10 000 meddelanden per sekund kan du prova [det här Azure-exempelprojektet](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

Dataflödet för Azure Cosmos DB-utdata är identiskt med 1.0 och 1.1. Vi *rekommenderar starkt* att du använder kompatibilitetsnivå 1.2 i Stream Analytics med Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Azure Cosmos DB-inställningar för JSON-utdata

Om du använder Azure Cosmos DB som utdata i Stream Analytics skapas följande uppmaning om information.

![Informationsfält för en Azure Cosmos DB-utdataström](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Field           | Beskrivning|
|-------------   | -------------|
|Utdataalias    | Ett alias som refererar till den här utdata i din Stream Analytics-fråga.|
|Prenumeration    | Azure-prenumerationen.|
|Konto-ID      | Namnet eller slutpunkten URI för Azure Cosmos DB-konto.|
|Kontonyckel     | Nyckeln för delad åtkomst för Azure Cosmos DB-kontot.|
|Databas        | Azure Cosmos DB-databasnamnet.|
|Containerns namn | Behållarnamnet, till `MyContainer`exempel . En behållare `MyContainer` som heter måste finnas.  |
|Dokument-ID     | Valfri. Kolumnnamnet i utdatahändelser som används som den unika nyckel som infogas eller uppdateras måste baseras på. Om du lämnar den tom infogas alla händelser utan uppdateringsalternativ.|

När du har konfigurerat Azure Cosmos DB-utdata kan du använda den i frågan som mål för en [INTO-sats](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). När du använder en Azure Cosmos DB-utdata på det sättet [måste en partitionsnyckel anges explicit](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Utdataposten måste innehålla en skiftlägeskänslig kolumn uppkallad efter partitionsnyckeln i Azure Cosmos DB. För att uppnå större parallellisering kan satsen kräva en [PARTITION BY-sats](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) som använder samma kolumn.

Här är en exempelfråga:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Felhantering och återförsök

Om ett tillfälligt fel, tjänstovailerbarhet eller begränsning inträffar medan Stream Analytics skickar händelser till Azure Cosmos DB, försöker Stream Analytics på obestämd tid för att slutföra åtgärden. Men det försöker inte försök igen för följande fel:

- Obehörig (HTTP-felkod 401)
- NotFound (HTTP-felkod 404)
- Förbjudet (HTTP-felkod 403)
- BadRequest (HTTP-felkod 400)
