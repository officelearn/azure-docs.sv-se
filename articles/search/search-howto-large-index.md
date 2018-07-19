---
title: Återskapa en Azure Search-index eller uppdatering sökbart innehåll | Microsoft Docs
description: Lägga till nya element, uppdatera befintliga element eller dokument eller ta bort föråldrade dokument i en fullständig återskapning eller partiella inkrementella indexering för att uppdatera ett Azure Search-index.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 0dd7a5d5159144c6b1a050ff4c0443b181976738
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124962"
---
# <a name="how-to-scale-out-indexing-in-azure-search"></a>Så här skalbar indexering i Azure Search

När datavolymer växer eller bearbetning behöver ändras, det kan hända att enkelt [behöver och omindexering jobb](search-howto-reindex.md) inte är tillräckligt. 

Som ett första steg mot att uppfylla ökade krav, rekommenderar vi att du ökar den [skalbarhet och kapacitet](search-capacity-planning.md) inom ramen för din befintliga tjänst. 

En andra steget, om du kan använda [indexerare](search-indexer-overview.md), lägger till mekanismer för skalbar indexering. Indexerare levereras med en inbyggd scheduler som låter dig kolli ut indexering med jämna mellanrum eller utöka bearbetning utöver 24-timmarsperiod. Dessutom tillsammans med definitioner av datakällor, indexerare hjälper dig att uppnå en form av parallellitet genom att partitionera data och använda scheman köra parallellt.

### <a name="scheduled-indexing-for-large-data-sets"></a>Schemalagd indexering för stora mängder data

Schemaläggning är ett viktigt mekanism för bearbetning av stora datamängder och långsamma analyser som bildanalys i en pipeline för kognitiv sökning. Indexeraren bearbetning fungerar inom en 24-timmarsperiod. Om bearbetning inte kan slutföras inom 24 timmar, arbeta funktioner för schemaläggning av indexerare till din fördel. 

Schemalagd indexering startar med ett visst intervall av design, med ett jobb som vanligtvis slutförs innan återupptas vid nästa schemalagda intervall. Om bearbetningen inte slutförs inom intervallet, stoppas dock indexeraren (eftersom tog slut tid). På nästa intervall spåra bearbetningen återupptar där den senast slutade, med system som av där som sker. 

Du kan placera indexeraren enligt ett schema i 24-timmarsformat rent praktiskt för index belastning över flera dagar. När indexering återupptar för nästa 24 timmars runda, startar om senaste kända dokumentet. På så sätt kan fungerar en indexerare genom en dokumentet eftersläpning över ett antal dagar tills alla obearbetade dokument behandlas. Mer information om den här metoden finns i [indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Parallell indexering

Ett andra alternativ är att ställa in en parallell indexering strategi. För icke-rutin, intensiv indexering förutsättningar, till exempel OCR på skannade dokument i en pipeline för kognitiv sökning, en parallell indexering strategi kan vara den rätta inställningen för den specifika mål. I en pipeline för kognitiv sökning berikande är bildanalys och bearbetning av naturligt språk långvariga. Parallell indexering för en tjänst som inte hanterar begäranden om indexfråga samtidigt kan det vara ett genomförbart alternativ för att arbeta via en stor mängd långsam bearbetning innehåll. 

En strategi för parallell bearbetning har dessa element:

+ Dela upp källdata mellan flera behållare eller flera virtuella mappar i samma behållare. 
+ Mappa varje mini datauppsättning till en [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source), tillsammans på en egen [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiv sökning, referera till samma [kompetens](https://docs.microsoft.com/rest/api/searchservice/create-skillset) i varje indexerarens definition.
+ Skriv till samma målsökindex. 
+ Schemalägga alla indexerare ska köras på samma gång.

> [!Note]
> Azure Search stöder inte tilldela repliker eller partitioner specifika arbetsbelastningar. Risken för tung samtidiga indexering är överbelastning systemet på bekostnad av prestanda för frågor. Om du har en testmiljö kan du implementera parallella indexering det först för att förstå kompromisser.

## <a name="configure-parallel-indexing"></a>Konfigurera parallella indexering

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