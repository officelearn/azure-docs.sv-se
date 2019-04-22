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
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495984"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics-utdata till Azure Cosmos DB  
Stream Analytics kan riktas mot [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) att aktivera arkivering och låg latens datafrågor för Ostrukturerade JSON-data för JSON-utdata. Det här dokumentet beskriver några av metodtipsen för att implementera den här konfigurationen.

För de som inte är bekant med Cosmos DB, ta en titt på [Utbildningsväg för Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) att komma igång. 

> [!Note]
> Just nu, Azure Stream Analytics endast stöd för anslutning till Azure Cosmos DB med hjälp av **SQL API**.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du punkt Azure Stream Analytics till Azure Cosmos DB-konton som skapats med andra API: er, kanske data inte korrekt lagras. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Grunderna i Cosmos DB som en utdatamål
Azure Cosmos DB-utdata i Stream Analytics kan skriva dina stream bearbetning resultat som JSON-utdata till Cosmos DB-samling(ar). Stream Analytics skapa inte samlingar i databasen, i stället kräver att du skapar dem i förskott. Det här är så att faktureringen kostnaderna för Cosmos DB-samlingar styrs av dig och så att du kan finjustera prestanda, konsekvens och kapaciteten för dina samlingar med direkt med hjälp av den [Cosmos DB API: er](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Du måste lägga till 0.0.0.0 i listan över tillåtna IP-adresser från din brandvägg för Azure Cosmos DB.

Några av alternativen för Cosmos DB-samling beskrivs nedan.

## <a name="tune-consistency-availability-and-latency"></a>Finjustera konsekvens, tillgänglighet och svarstid
För att matcha dina programkrav, kan Azure Cosmos DB du finjustera den databasen och samlingar och kompromissa mellan konsekvens, tillgänglighet och svarstid. Beroende på vilka nivåer av läsningskontinuitet behoven scenariot mot läser och skriver svarstid, du kan välja en konsekvensnivå på ditt databaskonto. Som standard kan Azure Cosmos DB också synkron indexering för varje CRUD-åtgärd i din samling. Det här är ett annat användbart alternativ för att styra skrivning/läsning prestanda i Azure Cosmos DB. Mer information finns i [ändra din databas och fråga konsekvensnivåer](../cosmos-db/consistency-levels.md) artikeln.

## <a name="upserts-from-stream-analytics"></a>Upsertar från Stream Analytics
Stream Analytics-integrering med Azure Cosmos DB kan du infoga eller uppdatera poster i din samling baserat på en viss dokument-ID-kolumn. Detta är kallas även en *Upsert*.

Stream Analytics använder en optimistisk upsert-metod, där uppdateringar utförs endast när insert misslyckas med ett dokument-ID-konflikt. Med kompatibilitet nivå 1.0 utförs uppdateringen som en uppdatering, så att den kan deluppdateringar till dokumentet, det vill säga, Lägg till nya egenskaper eller ersätta en befintlig egenskap utförs inkrementellt. Ändringar i värdena för matris egenskaper i JSON-dokument-resultat i hela matrisen komma över, det vill säga matrisen inte slagit samman. Med 1.2 ändras upsert beteendet för att infoga eller ersätta dokumentet. Det här är beskrivs mer i avsnittet kompatibilitet nivå 1.2 nedan.

Om inkommande JSON-dokumentet har ett befintligt ID-fält att fältet används automatiskt som dokument-ID-kolumn i Cosmos DB och alla efterföljande skrivningar hanteras därför leder till någon av dessa situationer:
- unika ID: N leda till att infoga
- dubbla ID: N och ”dokument-ID” inställd på ”ID” leder till upsert
- dubbla ID: N och dokument-ID inte set leder till fel, efter det första dokumentet

Om du vill spara <i>alla</i> dokument, inklusive som med en dubblett-ID, byta namn på ID-fältet i din fråga (med nyckelordet AS) och låt Cosmos DB skapa ID-fältet eller ersätta ID med en annan kolumn värde (med hjälp av nyckelordet AS eller med inställningen dokument-ID).

## <a name="data-partitioning-in-cosmos-db"></a>Datapartitionering i Cosmos DB
Azure Cosmos DB [obegränsad](../cosmos-db/partition-data.md) behållare är den rekommenderade metoden för att partitionera dina data, som Azure Cosmos DB automatiskt skalar partitioner baserat på din arbetsbelastning. Vid skrivning till obegränsade behållare, använder Stream Analytics så många parallella skrivare som föregående fråga steg eller indata partitioneringsschema.
> [!Note]
> För närvarande stöder Azure Stream Analytics endast obegränsade samlingar med partitionsnycklar på den översta nivån. Till exempel `/region` stöds. Kapslade partitionsnycklar (t.ex. `/region/name`) stöds inte. 

För fasta Azure Cosmos DB-samlingar kan Stream Analytics inget sätt att skala upp eller ut när de är full. De har en övre gräns på 10 GB och 10 000 RU/s genomströmning.  Om du vill migrera data från en fast behållare till en obegränsad behållare (till exempel en med minst 1 000 RU/s och en partitionsnyckel), måste du använda den [datamigreringsverktyget](../cosmos-db/import-data.md) eller [ändringsfeed biblioteket](../cosmos-db/change-feed.md).

Skrivning till flera fast behållare tas ur bruk och är inte den rekommenderade metoden för att skala ut ditt Stream Analytics-jobb. Artikeln [partitionering och skalning i Cosmos DB](../cosmos-db/sql-api-partition-data.md) innehåller ytterligare information.

## <a name="improved-throughput-with-compatibility-level-12"></a>Förbättrad dataflöde med kompatibilitet nivå 1.2
Stream Analytics har stöd för intern integrering vill skriva till Cosmos DB med kompatibilitetsnivå 1.2. På så sätt kan skriva ett effektivt sätt till Cosmos DB med Maximera dataflöde och mer effektivt hantera begränsningar förfrågningar. Mekanismen för förbättrad skrivning är tillgänglig under en ny kompatibilitetsnivå på grund av en upsert beteende skillnad.  Innan du 1.2 är upsert-funktionen att infoga eller sammanfoga dokumentet. Med 1.2 ändras upsertar funktionen för att infoga eller ersätta dokumentet. 

Innan du 1.2, använder du en anpassad lagrad procedur till bulk upsert dokument per partitionsnyckel till Cosmos DB, där en batch skrivs som en transaktion. Även om en enskild post når ett tillfälligt fel (begränsningen), måste hela batchen utföras igen. Detta görs scenarier med relativt långsammare även rimliga begränsning. Följande jämförelse visar hur sådana jobb skulle fungera med 1.2.

Visar två identiska Stream Analytics-jobb med att läsa från samma indata (händelsehubb) under installationen. Båda Stream Analytics-jobb är [fullständigt partitionerad](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) med en genomströmning fråga och -skrivning till identiska CosmosDB-samlingar. Mått till vänster från jobbet som konfigurerats med kompatibilitetsnivå 1.0 och de till höger är konfigurerad med 1.2. Partitionsnyckeln för cosmos DB-samlingar är ett unikt guid som kommer från den inkommande händelsen.

![jämförelse av Stream analytics-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Mottagningshastighet händelse i Event Hub är 2 x högre än Cosmos DB-samlingar (20K ru: er) är konfigurerade för att hämta, så begränsning förväntas i Cosmos DB. Jobbet med 1.2, skriver dock konsekvent med ett högre dataflöde (utdata händelser/minut) och med en lägre genomsnittlig SU % utnyttjande. I din miljö beror den här skillnaden på några flera faktorer, till exempel val av event format, inkommande händelsemeddelandet storlek, partitionsnycklar, fråga osv.

![jämförelse av cosmos db-mått](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Med 1.2 är Stream Analytics smartare hur man kan använda 100% av det tillgängliga genomflödet i Cosmos DB med mycket få resubmissions från begränsning/hastighetsbegränsning. Detta ger en bättre upplevelse för andra arbetsbelastningar som frågor som körs på en samling på samma gång. Om du behöver att testa hur ASA skalas ut med Cosmos DB som en mottagare för 1k till 10k meddelanden per sekund, här är en [azure-exempel projektet](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) som kan du göra detta.
Observera att Cosmos DB utdatagenomflöde är identiska med 1.0 och 1.1. Eftersom 1.2 för närvarande inte är standard, kan du [Ange kompatibilitetsnivån](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) för ett Stream Analytics-jobb med hjälp av portalen eller med hjälp av den [skapa jobbet REST API-anrop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Den har *rekommenderar vi* att använda kompatibilitet nivå 1.2 i ASA med Cosmos DB. 



## <a name="cosmos-db-settings-for-json-output"></a>Cosmos DB-inställningar för JSON-utdata

Skapar Cosmos DB som utdata i Stream Analytics genererar en uppmaning information som visas nedan. Det här avsnittet innehåller en förklaring av egenskaper-definition.

![documentdb stream analytics utdata skärmen](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Fält           | Beskrivning|
|-------------   | -------------|
|Utdataalias    | Ett alias till se detta utdata i din ASA-fråga.|
|Prenumeration    | Välj den Azure-prenumerationen.|
|Konto-ID      | Namn eller slutpunkten URI: N för Azure Cosmos DB-kontot.|
|Kontonyckel     | Den delade åtkomstnyckeln för Azure Cosmos DB-kontot.|
|Databas        | Namnet på Azure Cosmos DB-databasen.|
|Samlingsnamnsmönster | Samlingsnamn för samlingen som ska användas. `MyCollection` är en giltig inmatning för exemplet – en samling som heter `MyCollection` måste finnas.  |
|Dokument-id     | Valfri. Kolumnnamnet i utdatahändelserna används som den unika nyckeln åtgärder måste baseras på vilken insert eller update. Om fältet lämnas tomt kommer alla händelser att infogas, där du inte uppdateringen.|
