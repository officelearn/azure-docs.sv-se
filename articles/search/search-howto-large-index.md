---
title: Indexera stora datauppsättningar med hjälp av inbyggda indexerare
titleSuffix: Azure Cognitive Search
description: Strategier för stor dataindexering eller beräkningsintensiv indexering genom batchläge, resurs och tekniker för schemalagd, parallell och distribuerad indexering.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190968"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Indexera stora datauppsättningar i Azure Cognitive Search

När datavolymerna växer eller bearbetningsbehoven ändras kan det hända att enkla eller standardindexeringsstrategier inte längre är praktiska. För Azure Cognitive Search finns det flera metoder för att ta emot större datauppsättningar, allt från hur du strukturerar en begäran om dataöverföring till att använda en källspecifik indexerare för schemalagda och distribuerade arbetsbelastningar.

Samma tekniker gäller även för långvariga processer. De steg som beskrivs i [parallell indexering](#parallel-indexing) är särskilt användbara för beräkningsintensiv indexering, till exempel bildanalys eller bearbetning av naturligt språk i en [AI-anrikningspipeline](cognitive-search-concept-intro.md).

I följande avsnitt undersöks tre tekniker för indexering av stora mängder data.

## <a name="option-1-pass-multiple-documents"></a>Alternativ 1: Skicka flera dokument

En av de enklaste mekanismerna för att indexera en större datauppsättning är att skicka flera dokument eller poster i en enda begäran. Så länge hela nyttolasten är under 16 MB kan en begäran hantera upp till 1 000 dokument i en massöverföring. Dessa gränser gäller oavsett om du använder [REST-API:et för Lägg till dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) eller [indexmetoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) i .NET SDK. För båda API:na paketerar du 1 000 dokument i brödtexten för varje begäran.

Batchindexering implementeras för enskilda begäranden med REST eller .NET, eller via indexerare. Några indexerare verkar under olika gränser. Azure Blob-indexering anger batchstorlek till 10 dokument som ett erkännande av den större genomsnittliga dokumentstorleken. För indexerare som baseras på [REST-API:et Skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)kan du ställa in argumentet för att anpassa den `BatchSize` här inställningen så att den bättre matchar egenskaperna för dina data. 

> [!NOTE]
> Om du vill hålla dokumentstorleken nere undviker du att lägga till data som inte kan ifrågasättas i ett index. Bilder och andra binära data är inte direkt sökbara och bör inte lagras i indexet. Om du vill integrera data som inte kan ifrågasättas i sökresultaten bör du definiera ett fält som inte är sökbart och som lagrar en URL-referens till resursen.

## <a name="option-2-add-resources"></a>Alternativ 2: Lägga till resurser

Tjänster som etableras på en av [standardprisnivåerna](search-sku-tier.md) har ofta underutnyttjad kapacitet för både lagring och arbetsbelastningar (frågor eller indexering), vilket gör [att öka partitionen och repliken räknas](search-capacity-planning.md) till en självklar lösning för att ta emot större datauppsättningar. För bästa resultat behöver du båda resurserna: partitioner för lagring och repliker för datainmatningsarbete.

Ökande repliker och partitioner är fakturerbara händelser som ökar din kostnad, men om du inte kontinuerligt indexerar under maximal belastning kan du lägga till skala för indexeringsprocessens varaktighet och sedan justera resursnivåerna nedåt efter indexeringen Färdiga.

## <a name="option-3-use-indexers"></a>Alternativ 3: Använd indexerare

[Indexerare](search-indexer-overview.md) används för att genomsöka Azure-datakällor som stöds för sökbart innehåll. Även om det inte är särskilt avsett för storskalig indexering, är flera indexeringsfunktioner särskilt användbara för att ta emot större datamängder:

+ Schedulers kan du paketera ut indexering med jämna mellanrum så att du kan sprida ut det över tiden.
+ Schemalagd indexering kan återupptas vid den senast kända stopppunkten. Om en datakälla inte genomsöks helt inom ett 24-timmarsfönster återupptas indexeringen dag två var den än slutade.
+ Partitionering av data i mindre enskilda datakällor möjliggör parallell bearbetning. Du kan dela upp källdata i mindre komponenter, till exempel i flera behållare i Azure Blob-lagring, och sedan skapa motsvarande, flera [datakällobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure Cognitive Search som kan indexeras parallellt.

> [!NOTE]
> Indexerare är data-källa-specifika, så med hjälp av en indexeringsmetod är endast lönsamt för valda datakällor på Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob storage](search-howto-indexing-azure-blob-storage.md), Tabell [lagring](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Schemalagd indexering

Indexer schemaläggning är en viktig mekanism för bearbetning av stora datamängder, samt långsamma processer som bildanalys i en kognitiv sökpipeline. Indexerbearbetningen fungerar inom ett 24-timmarsfönster. Om bearbetningen inte slutförs inom 24 timmar kan beteendena för indexeringsplanering fungera till din fördel. 

Schemaläggs med specifika intervall, med ett jobb som vanligtvis slutförs innan det återupptas vid nästa schemalagda intervall. Men om bearbetningen inte slutförs inom intervallet, stannar indexeraren (eftersom den tog slut). Vid nästa intervall återupptas bearbetningen där den senast slutade, med systemet att hålla reda på var det inträffar. 

I praktiken kan du placera indexeraren på ett 24-timmarsschema för indexbelastningar som sträcker sig över flera dagar. När indexeringen återupptas för nästa 24-timmarscykel startar den om vid det senast fungerande dokumentet. På så sätt kan en indexerare arbeta sig igenom en dokumenteftersläpning under en serie dagar tills alla obearbetade dokument bearbetas. Mer information om den här metoden finns i [Indexera stora datauppsättningar i Azure Blob storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Mer information om hur du anger scheman i allmänhet finns i [Skapa REST-API för indexerare](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) eller så här [schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallell indexering

En parallell indexeringsstrategi baseras på att indexera flera datakällor unisont, där varje datakälldefinition anger en delmängd av data. 

För icke-rutinmässiga, beräkningsintensiva indexeringskrav - till exempel OCR på skannade dokument i en kognitiv sökpipeline, bildanalys eller bearbetning av naturligt språk - är en parallell indexeringsstrategi ofta rätt metod för att slutföra en på kortast möjliga tid. Om du kan eliminera eller minska frågebegäranden är parallell indexering på en tjänst som inte hanterar frågor samtidigt det bästa strategialternativet för att arbeta igenom en stor mängd långsamt bearbetningsinnehåll. 

Parallell bearbetning har dessa element:

+ Dela upp källdata mellan flera behållare eller flera virtuella mappar i samma behållare. 
+ Mappa varje minidatauppsättning till sin egen [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source), parat med sin egen [indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiv sökning, referera till samma [skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) i varje indexeringsdefinition.
+ Skriv in i samma målsökindex. 
+ Schemalägg alla indexerare så att de körs samtidigt.

> [!NOTE]
> I Azure Cognitive Search kan du inte tilldela enskilda repliker eller partitioner till indexering eller frågebearbetning. Systemet bestämmer hur resurser används. För att förstå effekten på frågeprestanda kan du prova parallell indexering i en testmiljö innan du distribuerar den till produktion.  

### <a name="how-to-configure-parallel-indexing"></a>Konfigurera parallell indexering

För indexerare baseras bearbetningskapaciteten löst på ett indexerares delsystem för varje serviceenhet (SU) som används av söktjänsten. Flera samtidiga indexerare är möjliga på Azure Cognitive Search-tjänster som etablerats på grundläggande eller standardnivåer med minst två repliker. 

1. På sidan **Översikt** över söktjänsten i [Azure-portalen](https://portal.azure.com)kontrollerar du **prisnivån** för att bekräfta att den kan hantera parallell indexering. Både basic- och standardnivåer erbjuder flera repliker.

2. Öka [repliker](search-capacity-planning.md) för parallell bearbetning i **Inställningar** > **skala:** ytterligare en replik för varje indexeringsarbetsbelastning. Lämna ett tillräckligt antal för befintlig frågevolym. Att offra frågearbetsbelastningar för indexering är inte en bra kompromiss.

3. Distribuera data till flera behållare på en nivå som Azure Cognitive Search-indexerare kan nå. Det kan vara flera tabeller i Azure SQL Database, flera behållare i Azure Blob-lagring eller flera samlingar. Definiera ett datakällobjekt för varje tabell eller behållare.

4. Skapa och schemalägga flera indexerare så att de körs parallellt:

   + Anta en tjänst med sex repliker. Konfigurera sex indexerare, var och en mappad till en datakälla som innehåller en sjättedel av datauppsättningen för en 6-vägs delning av hela datauppsättningen. 

   + Peka varje indexerare på samma index. För kognitiva sökarbetsbelastningar pekar du varje indexerare på samma kompetens.

   + Schemalägg samma körningsmönster under varje indexering. Skapar till `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` exempel ett schema för 2018-05-15 på alla indexerare och körs med åttatimmarsintervall.

Vid den schemalagda tiden börjar alla indexerare körning, läser in data, använder enrichments (om du har konfigurerat en kognitiv sökpipeline) och skriver till indexet. Azure Cognitive Search låser inte indexet för uppdateringar. Samtidiga skrivningar hanteras, med återförsök om en viss skrivning inte lyckas vid första försöket.

> [!Note]
> När du ökar repliker bör du överväga att öka antalet partitioner om indexstorleken beräknas öka avsevärt. Partitioner lagrar segment med indexerat innehåll. ju fler partitioner du har, desto mindre segmentet var och en måste lagra.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database-indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Cognitive Search](search-security-overview.md)
