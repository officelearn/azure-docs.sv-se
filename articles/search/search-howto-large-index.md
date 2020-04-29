---
title: Indexera stor data uppsättning med inbyggda indexerare
titleSuffix: Azure Cognitive Search
description: Strategier för stor data indexering eller beräknings intensiv indexering via batch-läge, omkällor och tekniker för schemalagd, parallell och distribuerad indexering.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190968"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Så här indexerar du stora data uppsättningar i Azure Kognitiv sökning

Allteftersom data volymer växer eller bearbetningen behöver ändras kanske du upptäcker att enkla eller standard index strategier inte längre är praktiska. För Azure Kognitiv sökning finns det flera metoder för att få större data mängder, från hur du strukturerar en begäran om data uppladdning, för att använda en produktspecifik indexerare för schemalagda och distribuerade arbets belastningar.

Samma tekniker gäller även för långvariga processer. I synnerhet är de steg som beskrivs i [Parallel index](#parallel-indexing) användbara för beräknings intensiva indexering, till exempel bild analys eller naturligt språk bearbetning i en [AI-pipeline](cognitive-search-concept-intro.md).

I följande avsnitt lär du dig tre tekniker för att indexera stora mängder data.

## <a name="option-1-pass-multiple-documents"></a>Alternativ 1: skicka flera dokument

En av de enklaste mekanismerna för att indexera en större data uppsättning är att skicka flera dokument eller poster i en och samma begäran. Så länge hela nytto lasten är under 16 MB kan en begäran hantera upp till 1000 dokument i en Mass överförings åtgärd. Dessa begränsningar gäller oavsett om du använder [Lägg till dokument REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) eller [index-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) i .NET SDK. För båda API: erna kommer du att paketera 1000-dokument i bröd texten för varje begäran.

Batch-indexering implementeras för enskilda förfrågningar med hjälp av REST eller .NET, eller genom indexerare. Några indexerare använder olika gränser. Mer specifikt ställer Azure Blob-indexering batch-storlek vid 10 dokument i redovisningen av den större genomsnittliga dokument storleken. För indexerare som baseras på [skapa indexerare REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)kan du ange `BatchSize` argumentet för att anpassa den här inställningen för att bättre matcha egenskaperna för dina data. 

> [!NOTE]
> Undvik att lägga till icke-frågedata till ett index för att hålla nere dokument storleken. Bilder och andra binära data är inte direkt sökbara och bör inte lagras i indexet. Om du vill integrera data som inte går att köra i Sök resultaten bör du definiera ett fält som inte går att söka i som lagrar en URL-referens till resursen.

## <a name="option-2-add-resources"></a>Alternativ 2: Lägg till resurser

Tjänster som tillhandahålls på en av [standard pris nivåerna](search-sku-tier.md) har ofta underutnyttjad kapacitet för både lagring och arbets belastning (frågor eller indexering), vilket gör att en stor del av [partitionen och repliken räknas](search-capacity-planning.md) som en tydlig lösning för att kunna använda större data mängder. För bästa resultat behöver du båda resurserna: partitioner för lagring och repliker för att data ska kunna användas.

Att öka antalet repliker och partitioner är fakturerbara händelser som ökar din kostnad, men om du inte kontinuerligt indexerar under maximal belastning kan du lägga till skalning under indexerings processen och sedan justera resurs nivåerna nedåt när indexeringen är färdig.

## <a name="option-3-use-indexers"></a>Alternativ 3: Använd indexerare

[Indexerare](search-indexer-overview.md) används för att crawla Azure-datakällor som stöds för sökbart innehåll. Även om det är särskilt avsett för storskalig indexering är flera indexerings funktioner särskilt användbara för att kunna använda större data uppsättningar:

+ Med Schemaläggaren kan du regelbundet indexera index med jämna mellanrum så att du kan sprida ut det över tid.
+ Schemalagd indexering kan återupptas vid den senast kända stopp punkten. Om en data källa inte är fullständigt crawlad inom ett 24-timmarsformat, kommer indexeraren att återuppta indexeringen på dag två på var den slutade.
+ Att partitionera data i mindre enskilda data källor möjliggör parallell bearbetning. Du kan dela upp källdata i mindre komponenter, t. ex. i flera behållare i Azure Blob Storage, och sedan skapa motsvarande flera [data käll objekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure kognitiv sökning som kan indexeras parallellt.

> [!NOTE]
> Indexerare är data källa-/regionsspecifika, så användning av en indexerare-metod är endast livskraftig för valda data källor på Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Schemalagd indexering

Schemaläggning av indexering är en viktig mekanism för att bearbeta stora data mängder, samt långsamma processer som bild analys i en kognitiv Sök pipeline. Indexerings bearbetningen fungerar i 24-timmarsformat. Om bearbetningen inte kan slutföras inom 24 timmar kan beteendet för indexerings schemaläggningen fungera på din fördel. 

Enligt design börjar schemalagd indexering i bestämda intervall, med ett jobb som normalt slutförs innan nästa schemalagda intervall återupptas. Men om bearbetningen inte slutförs inom intervallet stoppas indexeraren (på grund av att tids gränsen uppnåddes). Vid nästa intervall återupptas bearbetningen där det senast slutade, med systemet som håller koll på var det inträffar. 

I praktiska villkor kan du använda ett 24-timmarsformat för index belastningar som sträcker sig över flera dagar. När indexeringen återupptas för nästa 24-timmars cykel, startas den om i det senast fungerande dokumentet. På så sätt kan en indexerare arbeta på ett sätt genom ett dokument efter släpning under en serie dagar tills alla obearbetade dokument har bearbetats. Mer information om den här metoden finns i [Indexera stora data uppsättningar i Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Mer information om hur du ställer in scheman i allmänhet finns i [skapa indexerare REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) eller se [hur du schemalägger indexerare för Azure kognitiv sökning](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallell indexering

En parallell indexerings strategi baseras på indexering av flera data källor i dem samtidigt, där varje data käll definition anger en delmängd av data. 

För icke-rutinmässigt, beräknings intensiva indexerings krav, till exempel OCR på skannade dokument i en kognitiv Sök pipeline, bild analys eller naturligt språk bearbetning – en parallell indexerings strategi är ofta den rätta metoden för att slutföra en långvarig process på kortast möjliga tid. Om du kan eliminera eller minska förfrågningar om förfrågningar är parallell indexering av en tjänst som inte samtidigt hanterar frågor det bästa strategi alternativet för att arbeta via en stor del av långsam bearbetning av innehåll. 

Parallell bearbetning har följande element:

+ Dela upp käll data mellan flera behållare eller flera virtuella mappar i samma behållare. 
+ Mappa varje mini-data till en egen [data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source), kopplade till sin egen [indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiv sökning refererar du till samma [färdigheter](https://docs.microsoft.com/rest/api/searchservice/create-skillset) i varje indexare-definition.
+ Skriv till samma mål Sök index. 
+ Schemalägg alla indexerare att köras samtidigt.

> [!NOTE]
> I Azure Kognitiv sökning kan du inte tilldela enskilda repliker eller partitioner för indexering eller bearbetning av frågor. Systemet fastställer hur resurser används. För att förstå påverkan på frågeresultat kan du prova parallell indexering i en test miljö innan du rullar den till produktions miljön.  

### <a name="how-to-configure-parallel-indexing"></a>Så här konfigurerar du parallell indexering

För indexerare är bearbetnings kapaciteten löst baserat på ett under system för indexerare för varje tjänst enhet (SU) som används av Sök tjänsten. Det går att använda flera samtidiga indexerare i Azure Kognitiv sökning-tjänster som är etablerade på Basic-eller standard-nivåerna med minst två repliker. 

1. På sidan **Översikt** för search service instrument panel på [Azure Portal](https://portal.azure.com)kontrollerar du **pris nivån** för att bekräfta att den kan hantera parallell indexering. Både Basic-och standard-nivån erbjuder flera repliker.

2. I **Inställningar** > **skala** [ökar du repliker](search-capacity-planning.md) för parallell bearbetning: ytterligare en replik för varje Indexer-arbetsbelastning. Lämna ett tillräckligt antal för den befintliga frågesträngen. Att offra frågor mot arbets belastningar för indexering är inte ett utmärkt kompromiss.

3. Distribuera data till flera behållare på en nivå som Azure Kognitiv sökning indexerare kan komma åt. Detta kan vara flera tabeller i Azure SQL Database, flera behållare i Azure Blob Storage eller flera samlingar. Definiera ett data käll objekt för varje tabell eller behållare.

4. Skapa och schemalägga flera indexerare som ska köras parallellt:

   + Anta en tjänst med sex repliker. Konfigurera sex indexerare, var och en mappad till en data källa som innehåller en-sjätte data uppsättning för en 6-vägs uppdelning av hela data uppsättningen. 

   + Peka varje indexerare till samma index. För kognitiva Sök arbets belastningar kan du peka varje indexerare till samma färdigheter.

   + I varje indexare-definition schemalägger du samma kör tids körnings mönster. Till exempel `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` skapas ett schema på 2018-05-15 på alla indexerare, som körs med åtta timmars intervall.

Vid den schemalagda tiden börjar alla indexerare köra, läsa in data, tillämpa berikar (om du har konfigurerat en kognitiv Sök pipeline) och skriver till indexet. Azure Kognitiv sökning låser inte indexet för uppdateringar. Samtidiga skrivningar hanteras med ett nytt försök om en viss skrivning inte lyckas vid första försöket.

> [!Note]
> När du ökar repliker bör du överväga att öka antalet partitioner om index storleken projiceras för att öka markant. Partitioner lagrar segment av indexerat innehåll. Ju fler partitioner du har, desto mindre är sektorn som var och en måste lagra.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Indexering i portalen](search-import-data-portal.md)
+ [Azure SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexeraren](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexeraren](search-howto-indexing-azure-tables.md)
+ [Säkerhet i Azure Kognitiv sökning](search-security-overview.md)
