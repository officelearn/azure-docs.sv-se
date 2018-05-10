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
ms.openlocfilehash: 006d04efb0a6bebc424cb005bf63af2b3cd7a42e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Hur du skapar ett Azure Search index

Bygga en index ändras dess struktur, ändra fysiska uttrycket för indexet i Azure Search-tjänsten. Omvänt kan är uppdatera ett index en endast innehåll uppdatering att hämta de senaste ändringarna från bidragande extern datakälla. Den här artikeln innehåller riktning om hur du uppdaterar index strukturellt och att.

Läs-och skrivbehörighet på servicenivån krävs för uppdateringar av index. Programmässigt, kan du anropa REST- eller .NET-API: er för en fullständig återskapa eller inkrementell indexering av innehåll, med parametrar som anger alternativ för klientuppdatering. 

I allmänhet är uppdateringar till ett index på begäran. Men för index fylls i med hjälp av specifik [indexerare](search-indexer-overview.md), du kan använda inbyggda Schemaläggaren. Schemaläggaren har stöd för dokumentet Uppdatera så ofta som var femtonde minut, upp till oavsett intervall och mönster som du behöver. En snabbare uppdateringsfrekvens kräver push-överföra index uppdateringar manuellt, kanske via en dubbel skrivning till transaktioner som uppdaterar både den externa datakällan och Azure Search index samtidigt.

## <a name="full-rebuilds"></a>Fullständig bygger

Återskapa krävs för många typer av uppdateringar. Återskapa avser borttagningen av ett index, både data och metadata, följt av sedan hämtar nytt index från externa datakällor. Programmässigt, [ta bort](https://docs.microsoft.com/rest/api/searchservice/delete-index), [skapa](https://docs.microsoft.com/rest/api/searchservice/create-index), och [ladda](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) indexet för att återskapa den. 

Efter återskapa, Kom ihåg att om du har testat frågemönster och bedömningen profiler, du kan förvänta dig variationen i frågeresultatet om det underliggande innehållet har ändrats.

## <a name="when-to-rebuild"></a>När du ska bygga

Planera för ofta, fullständig återskapas under active utvecklingen när indexet scheman är i ett tillstånd för som.

| Ändring | Återskapa status|
|--------------|---------------|
| Ändra ett fältnamn datatyp, eller dess [index-attribut](https://docs.microsoft.com/rest/api/searchservice/create-index) | Ändra en fältdefinition av vanligtvis ådrar sig en återskapning kan försämras, med undantag för dessa [index attribut](https://docs.microsoft.com/rest/api/searchservice/create-index): strängfält, SearchAnalyzer, SynonymMaps. Du kan lägga till attributen Retrievable, SearchAnalyzer och SynonymMaps till ett befintligt fält utan att behöva återskapa indexet.|
| Lägga till ett fält | Inga strikt krav på Återskapa. Befintliga indexerade dokument ges ett null-värde för det nya fältet. I en framtida omindexera läggs värden källdata till dokument. |
| Ta bort ett fält | Inga strikt krav på Återskapa. Ett borttaget fält används inte, men fysiskt fältdefinitionen och innehållet kvar i indexet förrän nästa återskapandet. |

> [!Note]
> Återskapning av en krävs också om du växlar nivåer. Om det finns ingen uppgradering på plats vid något tillfälle som du bestämmer dig för mer kapacitet. En ny tjänst måste skapas på den nya kapacitet punkten och index måste skapas från början på den nya tjänsten. 

## <a name="partial-or-incremental-indexing"></a>Inkrementell eller delvis indexering

När ett index i produktion kan fokus till inkrementell indexering, vanligtvis med några avbrott i discernable tjänsten. Inkrementell eller delvis indexering är en endast innehåll arbetsbelastning som synkroniserar innehåll för ett sökindex så att statusen för innehållet i en bidragande datakälla. Ett dokument som lagts till eller tas bort i källan läggs till eller tas bort i indexet. I koden anropar den [Lägg till, uppdatera och ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) åtgärden eller motsvarande .NET.

> [!Note]
> När du använder indexerare som crawlas externa datakällor kan utnyttjas ändringsspårning autentiseringsmekanismer i källsystem för inkrementell indexering. För [Azure Blob storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), en `lastModified` fältet används. På [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` har samma funktion. På liknande sätt kan både [Azure SQL Database indexeraren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) och [Azure Cosmos DB indexeraren](search-howto-index-cosmosdb.md#indexing-changed-documents) ha fält för flagga uppdateringar av rader. Läs mer om indexerare [indexeraren översikt](search-indexer-overview.md).

## <a name="scale-out-indexing"></a>Skalbar indexering

Som datavolymer öka eller bearbetning behöver ändras, kanske du upptäcker att enkelt återskapar och indexeringen jobben är inte tillräckligt. Som ett första steg mot att uppfylla ökade krav, rekommenderar vi att du ökar den [skalbarhet och kapacitet](search-capacity-planning.md) inom gränserna för din befintliga tjänst. 

Om du kan använda [indexerare](search-indexer-overview.md), ytterligare skalbar mekanismer blir tillgängliga. Indexerare levereras med en inbyggd Schemaläggaren som hjälper dig att kolli ut indexering med jämna mellanrum eller utöka bearbetning utanför fönstret 24 timmar. Dessutom när ihop med definitioner av datakällor hjälpa indexerare dig att uppnå en form av parallellitet genom partitionering data och använda scheman för att köra parallellt.

### <a name="scheduled-indexing-for-large-data-sets"></a>Schemalagda indexering för stora datamängder

Planering är ett viktigt används för bearbetning av stora datamängder och långsamma körs analyser som bildanalys i en kognitiva Sök-pipeline. Indexerare bearbetning fungerar inom en 24-timmars-fönstret. Om bearbetning inte kan slutföras inom 24 timmar, arbeta beteenden för schemaläggning av indexerare till din fördel. 

Schemalagd indexering startar med ett visst intervall av design, med ett jobb som vanligtvis Slutför innan återupptas vid nästa schemalagda intervall. Dock stoppar bearbetning inte slutförs inom intervallet, indexeraren (eftersom tog slut tid). Vid nästa intervall spåra bearbetning återupptar där den senast slutade, med system hålls av där som inträffar. 

Du kan placera indexeraren enligt ett schema för 24-timmarsformat rent praktiskt för index belastningar som sträcker sig över flera dagar. När indexering återupptar för nästa 24-timmarsformat stint startas om senast kända dokumentet. På så sätt kan fungerar en indexerare genom eftersläpande dokumentet över ett antal dagar tills alla obearbetade dokument bearbetas. Mer information om den här metoden finns [indexering stora datauppsättningar](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Parallell indexering

Ett andra alternativ är att skapa en parallell indexering strategi. För icke-rutin, beräkningsmässigt beräkningsintensiva indexering krav, t.ex OCR på skannade dokument i en kognitiva Sök pipeline en parallell indexering strategi kanske den rätta inställningen för den specifika mål. I en berikande pipeline kognitiva Sök är bildanalys och bearbetning av naturligt språk tidskrävande. Parallell indexering för en tjänst som inte hanterar frågebegäranden samtidigt kan det vara ett genomförbart alternativ för att arbeta med en stor samling långsamma bearbetning innehåll. 

En strategi för parallell bearbetning har följande element:

+ Dela upp källdata bland flera behållare eller flera virtuella mappar i samma behållare. 
+ Varje mini datauppsättning för att mappa en [datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source), parad till sin egen [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ För kognitiva sökning, referera till samma [kunskaper](ref-create-skillset.md) i varje definition av indexeraren.
+ Skriva till samma mål sökindexet. 
+ Schemalägga alla indexerare ska köras på samma gång.

> [!Note]
> Azure Search stöder inte trafikklass repliker och partitioner för specifika arbetsbelastningar. Risken för tunga samtidiga indexering är överbelastning systemet på bekostnad av frågeprestanda. Om du har en testmiljö kan du implementera parallella indexering det först för att förstå kompromisser.

### <a name="configure-parallel-indexing"></a>Konfigurera parallella indexering

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