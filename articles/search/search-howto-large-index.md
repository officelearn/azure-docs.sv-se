---
title: Index stor datauppsättning med hjälp av inbyggda indexerare – Azure Search
description: Lär dig strategier för stora mängder data som indexering eller intensiv indexering via batch-läge, resurser och tekniker för schemalagda, parallella och distribuerade indexering.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2f3d08a32384cea815f096f51b24eea596d0d118
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742243"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>Indexera stora datauppsättningar i Azure Search

När datavolymer växer eller bearbetning behöver ändras, kanske du upptäcker att indexering strategier som standard inte längre är praktiska. Det finns flera metoder för att ta emot större datamängder bör sträcker sig från hur du strukturerar data överföringsförfrågan, med en specifik indexeraren för schemalagda och distribuerade arbetsbelastningar för Azure Search.

Samma teknik för stora mängder data gäller även för tidskrävande processer. I synnerhet steg som beskrivs i [parallella indexering](#parallel-indexing) är användbara för intensiv indexering, till exempel bildanalys eller naturlig språkbearbetning i [kognitiv sökning pipelines](cognitive-search-concept-intro.md).

## <a name="batch-indexing"></a>Batch-indexering

En av de enklaste mekanismerna för indexering av en större mängd data är att skicka flera dokument eller poster i en enskild begäran. Så länge hela nyttolasten är under 16 MB, kommer en begäran kan hantera upp till 1 000 dokument i en bulkåtgärd för överföring. Förutsatt att den [Lägg till eller uppdatera dokument REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), skulle du paketera 1000 dokument i brödtexten i begäran.

Batch indexering implementeras för enskilda begäranden med REST eller .NET eller via indexerare. Några indexerare arbeta under olika begränsningar. Mer specifikt anger Azure Blob-indexering batchstorlek vid 10 dokument uppmärksamma större genomsnittlig dokumentstorlek. För indexerare baserat på den [skapa indexeraren REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ), du kan ange den `BatchSize` argumentet att anpassa den här inställningen för att matcha egenskaperna för dina data. 

> [!NOTE]
> Kom ihåg att undanta inte kan frågas data från begäran för att hålla dokumentstorlek nere. Bilder och andra binära data är inte direkt sökbart och bör inte lagras i indexet. Om du vill integrera inte kan frågas data i sökresultat, bör du definiera en icke-sökbart fält som lagrar en URL: en referens till resursen.

## <a name="add-resources"></a>Lägg till resurser

Tjänster som är etablerade på en av de [Standard prisnivåer](search-sku-tier.md) har ofta outnyttjade kapacitet för både lagring och arbetsbelastningar (frågor eller indexering), vilket gör [öka antal partition och repliken ](search-capacity-planning.md) en uppenbar lösning för att ta emot större datauppsättningar. För bästa resultat måste båda resurserna: partitioner för lagring och repliker för datainmatning fungerar.

Ökande repliker och partitioner är faktureringsbara händelser som ökar dina kostnader, men om inte du kontinuerligt indexerar vid maximal belastning, du Lägg till skala under hela indexeringsprocessen och sedan justera resursnivåer nedåt när indexeringen klar.

## <a name="use-indexers"></a>Använd indexerare

[Indexerare](search-indexer-overview.md) används för att crawlar externa datakällor för sökbart innehåll. Även om den inte specifikt för storskaliga indexering, är flera indexerare funktioner särskilt användbara för större datauppsättningar:

+ Schemaläggare för kan du kolli ut indexering regelbundet så att du kan sprida den ut över tid.
+ Schemalagda indexering kan återuppta vid tidpunkten för senaste kända stoppas. Om en datakälla inte är fullständigt crawla inom en 24-timmarsperiod, återupptas indexeraren indexering av dag två på var den avbröts.
+ Partitionera data i mindre enskilda datakällor kan parallell bearbetning. Du kan dela upp en stor datauppsättning i mindre datauppsättningar och skapa sedan flera definitioner av datakällor som kan indexeras parallellt.

> [!NOTE]
> Indexerare är data-specifik, så med hjälp av en indexerare metod är bara användbara för valda datakällor på Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob-lagring](search-howto-indexing-azure-blob-storage.md), [tabellagring](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

## <a name="scheduled-indexing"></a>Schemalagd indexering

Indexeraren schemaläggning är ett viktigt mekanism för bearbetning av stora datamängder, samt långsamma processer som bildanalys i en pipeline för kognitiv sökning. Indexeraren bearbetning fungerar inom en 24-timmarsperiod. Om bearbetning inte kan slutföras inom 24 timmar, arbeta funktioner för schemaläggning av indexerare till din fördel. 

Schemalagd indexering startar med ett visst intervall av design, med ett jobb som vanligtvis slutförs innan återupptas vid nästa schemalagda intervall. Om bearbetningen inte slutförs inom intervallet, stoppas dock indexeraren (eftersom tog slut tid). På nästa intervall spåra bearbetningen återupptar där den senast slutade, med system som av där som sker. 

Du kan placera indexeraren enligt ett schema i 24-timmarsformat rent praktiskt för index belastning över flera dagar. När indexering återupptar tills nästa cykel i 24-timmarsformat, startar om senaste kända dokumentet. På så sätt kan fungerar en indexerare genom en dokumentet eftersläpning över ett antal dagar tills alla obearbetade dokument behandlas. Mer information om den här metoden finns i [indexering stora datauppsättningar i Azure Blob storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Läs mer om att ställa in scheman i allmänhet [skapa indexeraren REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Parallell indexering

En parallell indexering strategi baseras på indexering flera datakällor i dem samtidigt, där varje definition av datakällan anger en delmängd av data. 

För särskilda, intensiv indexering krav – till exempel OCR på skannade dokument i kognitiv sökning pipeline, bildanalys och bearbetning av naturligt språk – en parallell indexering strategi är ofta den rätta inställningen för att slutföra en tidskrävande process på kortast tid. Om du kan eliminera eller minska frågebegäranden, är parallella indexering för en tjänst som inte samtidigt hanterar frågor det bästa alternativet strategi för att arbeta via en stor mängd långsam bearbetning innehåll. 

Parallell bearbetning har dessa element:

+ Dela upp källdata mellan flera behållare eller flera virtuella mappar i samma behållare. 
+ Mappa varje mini datauppsättning till sin egen [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tillsammans på en egen [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiv sökning, referera till samma [kompetens](https://docs.microsoft.com/rest/api/searchservice/create-skillset) i varje indexerarens definition.
+ Skriv till samma målsökindex. 
+ Schemalägga alla indexerare ska köras på samma gång.

> [!NOTE]
> Azure Search stöder inte tilldela repliker eller partitioner specifika arbetsbelastningar. Risken för tung samtidiga indexering är överbelastning systemet på bekostnad av prestanda för frågor. Om du har en testmiljö kan du implementera parallella indexering det först för att förstå kompromisser.

### <a name="how-to-configure-parallel-indexing"></a>Så här konfigurerar du parallella indexering

För indexerare baseras bearbetning av kapacitet löst på en indexerare undersystem för varje tjänsteenhet (SU) som används av din söktjänst. Flera samtidiga indexerare är möjliga i Azure Search-tjänster som är etablerade på Basic eller Standard-nivåer som har minst två repliker. 

1. I den [Azure-portalen](https://portal.azure.com), på instrumentpanelen search **översikt** markerar den **prisnivå** att bekräfta att den kan hantera parallell indexering. Varken Basic eller Standard-nivån erbjuder flera repliker.

2. I **inställningar** > **skala**, [öka repliker](search-capacity-planning.md) för parallell bearbetning: ytterligare en replik för varje indexerare arbetsbelastning. Lämna tillräckligt många för den befintliga frågan volym. Det är inte en bra kompromiss att offra frågearbetsbelastningar för indexering.

3. Distribuera data i flera behållare på en nivå som Azure Search-indexerare kan nå. Detta kan vara flera tabeller i Azure SQL Database, flera behållare i Azure Blob storage eller flera samlingar. Definiera en datakällobjektet för varje tabell eller behållare.

4. Skapa och schemalägga flera indexerare parallell körning:

   + Anta att en tjänst med sex repliker. Konfigurera sex indexerare, var och en mappad till en datakälla som innehåller en-sjättedel av datauppsättningen för en 6-vägs delning av hela datauppsättningen. 

   + Peka varje indexerare till samma index. Peka varje indexerare på samma kompetens för kognitiv sökning arbetsbelastningar.

   + Schemalägga körning körning samma mönster inom varje indexerarens definition. Till exempel `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` skapar ett schema på 2018-05-15 på alla indexerare, som körs med åtta timmar intervall.

Alla indexerare börja köra, läsa in data, tillämpa enrichments (om du har konfigurerat en pipeline för kognitiv sökning) och skriva till indexet på den schemalagda tiden. Azure Search låser inte indexet för uppdateringar. Samtidiga skrivningar hanteras med försök igen om en viss skrivning inte lyckas på första försöket.

> [!Note]
> Om du vill öka repliker du överväga att öka antalet partitioner om Indexstorlek projiceras för att öka avsevärt. Partitioner lagrar sektorer från indexerade innehåll. fler partitioner som du har, desto mindre segment var och en har att lagra.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexeringen i portalen](search-import-data-portal.md)
+ [Azure SQL Database-indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Search](search-security-overview.md)