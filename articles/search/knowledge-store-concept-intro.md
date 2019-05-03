---
title: Knowledge Store introduktion och översikt – Azure Search
description: Skicka avancerad och dokument till Azure storage där du kan visa, omformar och använda avancerad och dokument i Azure Search och i andra program.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 3000016de934aaa3faab96821f9747ea4b571ef7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027000"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Vad är Knowledge Store i Azure Search?

Knowledge Store är en valfri funktion i Azure Search, för närvarande i förhandsversion, som sparar avancerad och dokument och metadata som skapats av en AI-baserade pipeline för fulltextindexering [(kognitiv sökning)](cognitive-search-concept-intro.md). Knowledge Store backas upp av ett Azure storage-konto som du konfigurerar som en del av pipelinen. När aktiverad använder söktjänsten det här lagringskontot för att cachelagra en representation av varje avancerad och dokument. 

Om du har använt kognitiv sökning tidigare vet du redan att kompetens kan användas för att flytta ett dokument via en sekvens av enrichments. Resultatet kan vara ett Azure Search-index eller (ny i den här förhandsversionen) projektioner i ett Arkiv för kunskap.

Projektioner är din mekanism för att strukturera data för användning i en underordnad app. Du kan använda [Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) byggd för Azure storage eller appar som ansluter till Azure storage, vilket öppnar nya möjligheter för att använda utökad dokument. Några exempel är data science-pipelines och anpassad analys.

![Knowledge Store i diagram med datapipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge Store i pipeline-diagram")

Om du vill använda Knowledge Store, lägger du till en `knowledgeStore` elementet så att en kompetens som definierar stegvis åtgärder i en pipeline för fulltextindexering. Under körningen Azure Search i Azure storage-kontot skapas ett blanksteg och fyller den med definitioner och innehåll som har skapats av pipelinen.

## <a name="benefits-of-knowledge-store"></a>Fördelarna med Knowledge Store

En knowledge store får du struktur, kontext och själva innehållet - finns i Ostrukturerade och delvis strukturerade datafiler så blobbar, bildfiler som har genomgått analys, eller även strukturerade data som har ändrat storlek i nya formulär. I en [stegvis beskrivning av](knowledge-store-howto.md) skrivna för den här förhandsversionen du kan se själv hur ett kompakt JSON-dokument är utpartitionerad till underordnade strukturer, färdigställts i nya strukturer och annars görs tillgängliga för nedströms processer som machine learning och data science-arbetsbelastningar.

Även om det är användbart för att se vad en AI-baserade indexering av pipeline kan ge är den verkliga kraften i Knowledge Store möjligheten att omformar dina data. Du kan börja med en grundläggande kompetens och sedan iterera över den för att lägga till ökande kontrollnivåer struktur som sedan kan du kombinera i nya strukturer, använda i andra appar utöver Azure Search.

Räkna upp, är fördelarna med Knowledge Store följande:

+ Använda avancerad och dokument i [analyser och rapporter verktyg](#tools-and-apps) än sökning. Powerbi med Power Query är ett intressant alternativ, men alla verktyg eller en app som kan ansluta till Azure storage kan hämta från en butik med kunskaper som du skapar.

+ Förfina en pipeline med AI-indexering när du felsöker steg och kompetens definitioner. En knowledge store visar produkten av en definition av kompetens i en pipeline med AI-indexering. Du kan använda dessa resultat för att utforma en bättre kompetens eftersom du kan se exakt hur enrichments ut. Du kan använda [Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) i Azure storage för att visa innehållet i en knowledge butik.

+ Forma data i nya formulär. Den Omforma är fastställs i kompetens men viktiga är att en kompetens nu kan du ange den här funktionen. Den [formaren färdighet](cognitive-search-skill-shaper.md) i Azure Search har utökats för att hantera den här uppgiften. Omforma kan du definiera en projektion som överensstämmer med avser att använda data och behålla relationer.

> [!Note]
> Inte bekant med AI-baserade indexering med kognitiva tjänster? Azure Search kan integreras med Cognitive Services visning och språk funktioner för att extrahera och utöka datakällan med hjälp av optisk teckenläsning (OCR) över bildfiler, entitetsidentifiering och extrahering av diskussionsämne från textfiler och mycket mer. Mer information finns i [vad är cognitive search?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Skapa ett Arkiv för kunskap

En knowledge store är en del av definitionen kompetens. I den här förhandsversionen kan skapa den kräver REST-API med hjälp av `api-version=2019-05-06-Preview` eller **dataimport** guiden i portalen.

Följande JSON anger en `knowledgeStore`, vilket är en del av en kompetens som anropas av en indexerare (visas inte). Specifikation av projektioner inom den `knowledgeStore` avgör huruvida tabeller eller objekt skapas i Azure storage.

Om du redan är bekant med AI-baserade indexering anger kompetens definitionen genereringen, organisation och ämne för varje avancerad och dokument.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Komponenter som backar upp ett arkiv med knowledge

Om du vill skapa en knowledge butik, behöver du följande tjänster och artefakter.

### <a name="1---source-data"></a>1 - Source data

Data eller dokument som ska utökas måste finnas i en Azure-datakälla som stöds av Azure Search-indexerare: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

[Azure tabellagring](search-howto-indexing-azure-tables.md) kan användas för utgående data i en knowledge store, men kan inte användas som en resurs för inkommande data till en AI-baserade indexering av pipeline.

### <a name="2---azure-search-service"></a>2 – azure Search-tjänst

Du behöver också en Azure Search-tjänst och REST-API för att skapa och konfigurera objekt som används för databerikande. REST-API för att skapa en knowledge store är `api-version=2019-05-06-Preview`.

Azure Search har funktionen indexeraren och indexerare som används för att driva den hela processen slutpunkt till slutpunkt, vilket resulterar i beständiga, avancerad och dokument i Azure storage. Indexerare använder en datakälla, ett index och en kompetens - som krävs för att skapa och fylla ett knowledge Arkiv.

| Object | REST-API | Beskrivning |
|--------|----------|-------------|
| datakälla | [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern Azure datakälla att tillhandahålla källdata som används för att skapa avancerad och dokument.  |
| Kompetens | [Skapa kompetens (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som samordna användningen av [inbyggda kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) används i en pipeline för berikande under indexering. |
| index | [Skapa Index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycker ett Azure Search-index. Fält i indexet mappar till fält i datakällan eller fält som tillverkade under fasen för berikande (t.ex, ett fält för organisationsnamn som skapats av entitetsidentifiering). |
| indexerare | [Skapa indexerare (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som definierar komponenter som används under indexering: som en datakälla, en kompetens, fältkopplingar från käll- och mellanliggande datastrukturer till målindex och indexet själva. Köra indexeraren är en utlösare för datainmatning och funktioner. Resultatet är ett sökindex baserat på indexschemat fylls med källdata, berikats via kompetens.  |

### <a name="3---cognitive-services"></a>3 – cognitive Services

Enrichments som anges i en kompetens bygger på funktionerna för visuellt innehåll och språk i Cognitive Services. Cognitive Services-funktionerna utnyttjas under indexering via din kompetens. En kompetens är en sammansättning av färdigheter och kunskaper som är kopplade till specifika funktioner för visuellt innehåll och språk. Om du vill integrera Cognitive Services, kommer du [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md) till en kompetens.

### <a name="4---storage-account"></a>4 - storage-konto

Under ditt Azure Storage-konto skapar en Blob-behållare eller tabeller, beroende på hur du konfigurerar en kompetens i Azure Search. Om dina data samlas in från Azure Blob eller Table storage, anger du redan. I annat fall behöver du skapa ett Azure storage-konto. Tabeller och objekt i Azure storage innehåller avancerad och dokument som skapats i för AI-baserade indexering av pipeline.

Storage-konto har angetts i gruppens kunskaper avgör. I `api-version=2019-05-06-Preview`, en kompetens definition innehåller en definition av knowledge store så att du kan ange kontoinformation.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 – åtkomst till och använda

När enrichments finns i lagring, kan alla verktyg eller teknik som ansluter till Azure Blob eller Table storage användas för att utforska, analysera eller använda innehållet. I följande lista är en start:

+ [Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) Visa avancerad och dokumentstruktur och innehåll. Tänk på detta som din baslinje-verktyg för att visa knowledge innehållet.

+ [Power BI med Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) för frågor med naturligt språk eller Använd rapporter och analyser verktyg om du har numeriska data.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) för ytterligare manipulering.

+ Azure Search-index för fulltextsökning över innehåll som du har indexerat med [kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Dokumentet persistence

Storage-konto kan enrichments uttryckas som tabeller i Azure-tabellagring eller objekt i Azure Blob storage. Kom ihåg att enrichments, lagras en gång, kan användas som källa för att läsa in data till andra databaser och verktyg,

+ Table storage är användbart när du vill ha en schema-medvetna representation av data i tabellformat. Om du vill omforma eller kombinera element på nya sätt ger tabellagring nödvändiga precisionen.

+ BLOB-lagring skapar en heltäckande JSON-representation av varje dokument. Du kan använda båda lagringsalternativen i en kompetens för att få en fullständig uppsättning uttryck.

+ Azure Search kvarstår innehållet i ett index. Om ditt scenario är icke-sökrelaterade, till exempel om ditt mål är analyser i något annat verktyg, kan du ta bort det index som pipelinen skapar. Men du kan också hålla indexet och använda ett inbyggda verktyg som [Sökutforskaren](search-explorer.md) som ett tredje medium (bakom Storage Explorer och analytics-app) för att interagera med innehållet.

Avancerad och dokument innehålla metadata för kompetens-version som producerade enrichments tillsammans med innehållet i dokumentet.  

## <a name="inside-a-knowledge-store"></a>I ett Arkiv för kunskap

Arkivet knowledge består av en anteckning cache och projektioner. Den *cache* används av tjänsten internt för att cachelagra resultaten från kunskaper och spårning av ändringar. En *projektion* definierar schemat och strukturen för enrichments som matchar den tänkta användningen. Det finns en cache per knowledge store, men flera projektioner. 

Cacheminnet är alltid en blob-behållare, men kan vara uppvisat projektioner som tabeller eller objekt:

+ Projektionen som mappar till Blob-lagring, där projektionen har sparats till en behållare som är de objekt eller en hierarkisk representation i JSON för scenarier som en datavetenskapspipeline som ett-objekt.

+ Projektionen som mappar till tabellagring som en tabell. En tabular representation bevarar relationer för scenarier som dataanalys eller export som dataramar för machine learning. Avancerad och projektioner kan sedan importeras enkelt till andra datalager. 

Du kan skapa flera projektioner i en knowledge store att hantera olika constituencies i din organisation. Utvecklare kan ha åtkomst till den fullständiga JSON-representationen av en avancerad och dokument, medan datavetare eller analytiker kanske vill detaljerade eller modulära datastrukturer formade genom din kompetens.

Till exempel om ett av målen med berikande processen är att också skapa en datauppsättning som används för att träna en modell, blir projicerar data till arkivet objektet ett sätt att använda data i dina data science-pipelines. Du kan också om du vill skapa en snabb Power BI-instrumentpanel baserat på Avancerad och dokumenten skulle tabular projektionen fungera bra.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Vad ska jag börja med?

Vi rekommenderar den kostnadsfria tjänsten i utbildningssyfte, men tänk på att antalet kostnadsfria transaktioner är begränsad till 20 dokument per dag, per prenumeration.

När du använder flera tjänster kan du skapa alla dina tjänster i samma region för bästa prestanda och för att minimera kostnaderna. Du debiteras inte för bandbredd för inkommande eller utgående data som går till en annan tjänst i samma region.

**Steg 1: [Skapa en Azure Search-resurs](search-create-service-portal.md)** 

**Steg 2: [Skapa ett Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Steg 3: [Skapa en resurs för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Steg 4: [Kom igång med portalen](cognitive-search-quickstart-blob.md) - eller - [Kom igång med exempeldata med hjälp av REST och Postman](knowledge-store-howto.md)** 

Du kan använda REST `api-version=2019-05-06-Preview` att konstruera en AI-baserade-pipeline som innehåller Knowledge Store. I den senaste förhandsversionen API kompetens-objektet innehåller de `knowledgeStore` definition.

## <a name="takeaways"></a>Lärdomar

Knowledge Store erbjuder en mängd fördelar, inklusive men inte begränsat till att aktivera användning av avancerad och dokument i scenarier än sökning, kostnad kontroller och hantera drift i berikande-processen. Dessa funktioner är tillgängliga att använda enkelt genom att lägga till ett lagringskonto i din kompetens och med den uppdaterade Uttrycksspråk enligt beskrivningen i [hur du kommer igång med Knowledge Store](knowledge-store-howto.md). 

## <a name="next-steps"></a>Nästa steg

Den enklaste metoden för att skapa avancerad och dokument är via den **dataimport** guiden.

> [!div class="nextstepaction"]
> [Snabbstart: Prova cognitive search i en genomgång av portalen](cognitive-search-quickstart-blob.md)
