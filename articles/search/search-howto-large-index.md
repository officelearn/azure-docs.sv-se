---
title: Återskapa ett Azure Search index eller uppdatera sökbara innehåll | Microsoft Docs
description: Lägga till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en återskapa eller partiell inkrementell indexering för att uppdatera ett Azure Search-index.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655712"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Så här skalbar indexering i Azure Seearch

Eftersom datavolymer växer eller bearbetning behöver ändras, det kan hända att enkelt [bygger och indexeringen jobb](search-howto-reindex.md) inte är tillräckligt. 

Som ett första steg mot att uppfylla ökade krav, rekommenderar vi att du ökar den [skalbarhet och kapacitet](search-capacity-planning.md) inom gränserna för din befintliga tjänst. 

Nästa steg om du kan använda [indexerare](search-indexer-overview.md), lägger till mekanismer för skalbar indexering. Indexerare levereras med en inbyggd Schemaläggaren som hjälper dig att kolli ut indexering med jämna mellanrum eller utöka bearbetning utanför fönstret 24 timmar. Dessutom när ihop med definitioner av datakällor hjälpa indexerare dig att uppnå en form av parallellitet genom partitionering data och använda scheman för att köra parallellt.

### <a name="scheduled-indexing-for-large-data-sets"></a>Schemalagda indexering för stora datamängder

Planering är ett viktigt används för bearbetning av stora datamängder och långsamma körs analyser som bildanalys i en kognitiva Sök-pipeline. Indexerare bearbetning fungerar inom en 24-timmars-fönstret. Om bearbetning inte kan slutföras inom 24 timmar, arbeta beteenden för schemaläggning av indexerare till din fördel. 

Schemalagd indexering startar med ett visst intervall av design, med ett jobb som vanligtvis Slutför innan återupptas vid nästa schemalagda intervall. Dock stoppar bearbetning inte slutförs inom intervallet, indexeraren (eftersom tog slut tid). Vid nästa intervall spåra bearbetning återupptar där den senast slutade, med system hålls av där som inträffar. 

Du kan placera indexeraren enligt ett schema för 24-timmarsformat rent praktiskt för index belastningar som sträcker sig över flera dagar. När indexering återupptar för nästa 24-timmarsformat stint startas om senast kända dokumentet. På så sätt kan fungerar en indexerare genom eftersläpande dokumentet över ett antal dagar tills alla obearbetade dokument bearbetas. Mer information om den här metoden finns [indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Parallell indexering

Ett andra alternativ är att skapa en parallell indexering strategi. För icke-rutin, beräkningsmässigt beräkningsintensiva indexering krav, t.ex OCR på skannade dokument i en kognitiva Sök pipeline en parallell indexering strategi kanske den rätta inställningen för den specifika mål. I en berikande pipeline kognitiva Sök är bildanalys och bearbetning av naturligt språk tidskrävande. Parallell indexering för en tjänst som inte hanterar frågebegäranden samtidigt kan det vara ett genomförbart alternativ för att arbeta med en stor samling långsamma bearbetning innehåll. 

En strategi för parallell bearbetning har följande element:

+ Dela upp källdata bland flera behållare eller flera virtuella mappar i samma behållare. 
+ Varje mini datauppsättning för att mappa en [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source), parad till sin egen [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiva sökning, referera till samma [kunskaper](https://docs.microsoft.com/rest/api/searchservice/create-skillset) i varje definition av indexeraren.
+ Skriva till samma mål sökindexet. 
+ Schemalägga alla indexerare ska köras på samma gång.

> [!Note]
> Azure Search stöder inte trafikklass repliker och partitioner för specifika arbetsbelastningar. Risken för tunga samtidiga indexering är överbelastning systemet på bekostnad av frågeprestanda. Om du har en testmiljö kan du implementera parallella indexering det först för att förstå kompromisser.

## <a name="configure-parallel-indexing"></a>Konfigurera parallella indexering

För indexerare baseras processorkapaciteten löst på en indexerare undersystemet för varje service-enhet (SU) som används av din söktjänst. Flera samtidiga indexerare är möjliga för Azure Search-tjänster som har etablerats på Basic eller Standard nivåer med minst två repliker. 

1. I den [Azure-portalen](https://portal.azure.com), på instrumentpanelen search-tjänsten **översikt** sida, kontrollera den **prisnivå** att bekräfta att den kan hantera parallell indexering. Både Basic och Standard nivåer erbjuder flera repliker.

2. I **inställningar** > **skala**, [öka repliker](search-capacity-planning.md) för parallell bearbetning: ytterligare en replik för varje indexerare arbetsbelastning. Lämna tillräckligt många för den befintliga frågan volym. Det är inte en bra kompromiss att kompromissa frågan arbetsbelastningar för indexering.

3. Distribuera data till flera behållare till en nivå som Azure Search indexerare kan nå. Detta kan ha flera tabeller i Azure SQL Database, flera behållare i Azure Blob storage eller flera samlingar. Definiera en datakällobjektet för varje tabell eller behållare.

4. Skapa och schemalägga flera indexerare parallell körning:

   + Anta en tjänst med sex repliker. Konfigurera sex indexerare, var och en mappad till en datakälla som innehåller en sjätte av datamängden för en 6-vägs delning av alla data. 

   + Varje indexerare peka samma index. Peka varje indexerare för kognitiva Sök arbetsbelastningar på samma kunskaper.

   + Schemalägga körning körning av samma mönster i varje indexerare-definition. Till exempel `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` skapar ett schema på 2018-05-15 på alla indexerare som körs med åtta timmars intervall.

Alla indexerare börja körning och läser in data, tillämpa enrichments (om du har konfigurerat en kognitiva Sök pipeline) och skrivning till indexet på den schemalagda tiden. Azure Search låser inte indexet för uppdateringar. Samtidiga skrivningar hanteras med nytt försök om en viss skrivning inte lyckas vid första försöket.

> [!Note]
> Om du vill öka repliker du överväga att öka antalet partitioner om Indexstorlek förväntas avsevärt. Partitioner lagrar segment av indexerade innehåll. fler partitioner som du har, desto mindre segment var och en har att lagra.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Indexerare för Azure SQL-databas](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Search](search-security-overview.md)