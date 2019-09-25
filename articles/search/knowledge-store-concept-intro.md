---
title: Introduktion till Knowledge Store (för hands version) – Azure Search
description: Skicka berikade dokument till Azure Storage där du kan visa, ändra form på och använda berikade dokument i Azure Search och i andra program.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: ec0bf6002d8e90b41c2eed3c21f53e38f0fbbe8f
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265208"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Vad är kunskaps lager i Azure Search?

> [!Note]
> Kunskaps lagret är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Kunskaps Store är en funktion i Azure Search som sparar berikade dokument och metadata som skapats av en AI-baserad indexerings pipeline [(kognitiv sökning)](cognitive-search-concept-intro.md). Ett berikat dokument är en pipeline-utdata som skapas från innehåll som har extraherats, strukturer ATS och analyserats med hjälp av resurser i Cognitive Services. I en standard-AI-baserad pipeline är berikade dokument övergående, som endast används vid indexering och sedan ignoreras. Med kunskaps lager sparas dokument för efterföljande utvärdering, utforskning och kan bli indata till en underordnad data vetenskaps arbets belastning. 

Om du har använt kognitiv sökning tidigare vet du redan att färdighetsuppsättningar används för att flytta ett dokument genom en sekvens av anrikninger. Resultatet kan vara ett Azure Search-index eller (nytt i den här förhands granskningen) projektioner i ett kunskaps lager. De två utmatningarna, Sök indexet och kunskaps lagret, är fysiskt åtskilda från varandra. De delar samma innehåll, men lagras och används på olika sätt.

Fysiskt är ett kunskaps lager ett Azure Storage konto, antingen som Azure Table Storage, Blob Storage eller båda, beroende på hur du konfigurerar pipelinen. Alla verktyg eller processer som kan ansluta till Azure Storage kan använda innehållet i ett kunskaps lager.

Projektioner är din mekanism för att strukturera data i ett kunskaps lager. Genom projektioner kan du till exempel välja om utdata ska sparas som en enskild BLOB eller en samling relaterade tabeller. Ett enkelt sätt att visa innehållet i kunskaps lagret är genom den inbyggda [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) för Azure Storage.

![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Kunskaps lager i Pipeline-diagram")

Om du vill använda kunskaps lager `knowledgeStore` lägger du till ett-element i en färdigheter som definierar steg-för-steg-åtgärder i en indexerings pipeline. Under körningen skapar Azure Search ett utrymme i ditt Azure Storage-konto och projekt de sammanrika dokumenten med den definition som skapats i pipelinen.

## <a name="benefits-of-knowledge-store"></a>Fördelar med kunskaps lager

Ett kunskaps lager ger dig struktur, kontext och faktiskt innehåll – uppnår från ostrukturerade och delvis strukturerade datafiler som blobbar, bildfiler som har genomgått analyser eller till och med strukturerade data som har ändrats i nya formulär. I en [stegvis genom gång](knowledge-store-howto.md) som skrivs för den här för hands versionen kan du se hur ett kompakt JSON-dokument är partitionerat i under strukturer, rekonstituerat i nya strukturer och på annat sätt görs tillgängligt för efterföljande processer som dator arbets belastningar för inlärning och data vetenskap.

Även om det är användbart att se vad en AI-baserad indexerings pipeline kan skapa, är det möjligt att ändra form på data. Du kan börja med en grundläggande färdigheter och sedan iterera över den för att lägga till ökande struktur nivåer, som du sedan kan kombinera i nya strukturer, som kan förbrukas i andra appar förutom Azure Search.

Räknat, fördelarna med kunskaps lager inkluderar följande:

+ Använd omfattande dokument i andra [analys-och rapporterings verktyg](#tools-and-apps) än Sök. Power BI med Power Query är ett övertygande val, men alla verktyg och appar som kan ansluta till Azure Storage kan hämta från ett kunskaps lager som du skapar.

+ Förfina en AI-indexerings pipeline vid fel sökning av steg och färdigheter definitioner. I ett kunskaps lager visas produkten av en färdigheter-definition i en AI-indexerad pipeline. Du kan använda dessa resultat för att utforma en bättre färdigheter, eftersom du kan se exakt vad som kan se ut. Du kan använda [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) i Azure Storage för att visa innehållet i ett kunskaps lager.

+ Forma data till nya formulär. Omformningen är att kodifiera i färdighetsuppsättningar, men punkten är att en färdigheter nu kan tillhandahålla den här funktionen. [Formaren-kunskapen](cognitive-search-skill-shaper.md) i Azure Search har utökats för att kunna hantera den här uppgiften. Med hjälp av omformningen kan du definiera en projektion som anpassas efter den avsedda användningen av data medan du bevarar relationer.

> [!Note]
> Är du inte bekant med AI-baserad indexering med Cognitive Services? Azure Search integreras med Cognitive Services vision-och språk funktioner för att extrahera och utöka källdata med OCR (optisk tecken läsning) över bildfiler, entitets igenkänning och nyckel fras extrahering från textfiler med mera. Mer information finns i [Vad är kognitiv sökning?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Skapa ett kunskapslager

Ett kunskaps lager ingår i en färdigheter-definition. I den här för hands versionen måste du skapa REST API, `api-version=2019-05-06-Preview` med hjälp av eller guiden **Importera data** i portalen.

Följande JSON anger en `knowledgeStore`, som är en del av en färdigheter, som anropas av en indexerare (visas inte). Specifikationen av projektioner i `knowledgeStore` avgör om tabeller eller objekt skapas i Azure Storage.

Om du redan är bekant med AI-baserad indexering bestämmer färdigheter-definitionen skapande, organisation och ämne för varje berikat dokument.

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
               
            ]      
        },
        { 
            "tables": [ 
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

## <a name="components-backing-a-knowledge-store"></a>Komponenter som säkerhetskopierar ett kunskaps lager

Om du vill skapa ett kunskaps lager behöver du följande tjänster och artefakter.

### <a name="1---source-data"></a>1-källdata

De data eller dokument som du vill utöka måste finnas i en Azure-datakälla som stöds av Azure Search indexerare: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table Storage](search-howto-indexing-azure-tables.md)

### <a name="2---azure-search-service"></a>2-Azure Search tjänst

Du behöver också en Azure Search-tjänst och REST API för att skapa och konfigurera objekt som används för data anrikning. REST API för att skapa ett kunskaps lager `api-version=2019-05-06-Preview`är.

Azure Search tillhandahåller indexerings funktionen och indexerare används för att driva hela processen från slut punkt till slut punkt, vilket resulterar i bestående, omfattande dokument i Azure Storage. Indexerare använder en data källa, ett index och en färdigheter – alla krävs för att skapa och fylla i ett kunskaps lager.

| Object | REST-API | Beskrivning |
|--------|----------|-------------|
| datakälla | [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern Azure-datakälla som tillhandahåller källdata som används för att skapa dokument med omfattande data.  |
| färdigheter | [Skapa färdigheter (API-version = 2019-05 -06 – för hands version)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som koordinerar användningen av [inbyggda kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) som används i en anriknings pipeline vid indexering. |
| index | [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycker ett Azure Search-index. Fält i index kartan till fält i källdata eller till fält som tillverkas under anriknings fasen (till exempel ett fält för organisations namn som skapats av enhets igenkänning). |
| indexerare | [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som definierar komponenter som används vid indexering: inklusive en data källa, en färdigheter, fält associationer från käll-och mellanliggande data strukturer till mål index och själva indexet. Att köra indexeraren är utlösaren för data inmatning och berikning. Utdata är ett sökindex baserat på index schemat, ifyllt med källdata, berikade med färdighetsuppsättningar.  |

### <a name="3---cognitive-services"></a>3-Cognitive Services

De berikningar som anges i en färdigheter är antingen anpassade eller baserade på Visuellt innehåll-och språk funktionerna i Cognitive Services. Cognitive Services funktionerna utnyttjas under indexeringen genom din färdigheter. En färdigheter är en sammansättning av färdigheter och färdigheter är kopplade till specifika Visuellt innehåll-och språk funktioner. Om du vill integrera Cognitive Services [ansluter du en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md) till en färdigheter.

### <a name="4---storage-account"></a>4 – lagrings konto

Under ditt Azure Storage-konto skapar Azure Search en BLOB-behållare eller-tabeller eller båda, beroende på hur du konfigurerar projektioner i färdigheter. Om dina data kommer från Azure Blob eller Table Storage har du redan angett och kan återanvända lagrings kontot. Annars måste du skapa ett Azure Storage-konto. Tabeller och objekt i Azure Storage innehåller de berikade dokument som skapats av AI-baserade indexerings pipeliner.

Lagrings kontot anges i färdigheter. I `api-version=2019-05-06-Preview`innehåller en färdigheter-definition en kunskaps lager definition så att du kan ange konto information.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5-åtkomst och förbruka

När det finns lagrings utrymme kan alla verktyg och tekniker som ansluter till Azure Blob eller Table Storage användas för att utforska, analysera eller använda innehållet. Följande lista är en start:

+ [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) för att visa en omfattande dokument struktur och innehåll. Överväg detta som ditt bas linje verktyg för att Visa kunskaps lager innehåll.

+ [Power BI med Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) för frågor med naturligt språk, eller Använd verktygen för rapportering och analys om du har numeriska data.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) för ytterligare modifiering.

+ Azure Search index för full texts ökning över innehåll som du har indexerat med hjälp av [kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Dokument beständighet

I lagrings kontot kan berikarna uttryckas som tabeller i Azure Table Storage eller som objekt i Azure Blob Storage. Kom ihåg att när du har lagrat kan du använda den som källa för att läsa in data i andra databaser och verktyg.

+ Table Storage är användbart när du vill ha en schema medveten representation av data i tabell format. Om du vill forma om eller omkombinera element på nya sätt ger tabell lagring den nödvändiga precisionen.

+ Blob Storage skapar en allt-inkluderad JSON-representation av varje dokument. Du kan använda båda lagrings alternativen i en färdigheter för att få ett fullständigt spektrum av uttryck.

+ Azure Search behåller innehållet i ett index. Om ditt scenario inte är sökrelaterat, till exempel om målet är analys i ett annat verktyg, kan du ta bort indexet som pipelinen skapar. Men du kan också behålla indexet och använda ett inbyggt verktyg som [Sök Utforskaren](search-explorer.md) som ett tredje medium (bakom Storage Explorer och din analys app) för att interagera med ditt innehåll.  

## <a name="inside-a-knowledge-store"></a>I ett kunskaps lager

 En *projektion* definierar schemat och strukturen för de anrikninger som matchar den avsedda användningen. Du kan definiera flera projektioner om du har program som använder data i olika format och former. 

Projektioner kan ledas som objekt eller tabeller:

+ Som ett objekt mappar projektionen till Blob Storage, där projektionen sparas i en behållare, inom vilka är objekt eller hierarkiska representationer i JSON för scenarier som en data vetenskaps pipeline.

+ Som en tabell mappas projektionen till Table Storage. En tabell representation bevarar relationer för scenarier som data analys eller exportera som data ramar för maskin inlärning. De berikade projektionerna kan sedan enkelt importeras till andra data lager. 

Du kan skapa flera projektioner i ett kunskaps lager för att hantera olika constituencies i din organisation. En utvecklare kan behöva åtkomst till den fullständiga JSON-representationen av ett berikat dokument, medan data forskare eller analytiker kan ha detaljerade eller modulära data strukturer som är formade för din färdigheter.

Om ett av målen för anriknings processen exempelvis är att skapa en data uppsättning som används för att träna en modell, är det ett sätt att projicera data i objekt arkivet på ett sätt att använda data i dina pipelines för data vetenskap. Alternativt, om du vill skapa en snabb Power BI instrument panel baserat på de omfattande dokumenten, skulle tabell projektionen fungera bra.

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

Vi rekommenderar den kostnads fria tjänsten för inlärnings syfte, men tänk på att antalet kostnads fria transaktioner är begränsat till 20 dokument per dag, per prenumeration.

När du använder flera tjänster kan du skapa alla dina tjänster i samma region för bästa prestanda och minimera kostnaderna. Du debiteras inte för bandbredd för inkommande data eller utgående data som går till en annan tjänst i samma region.

**Steg 1: [Skapa en Azure Search resurs](search-create-service-portal.md)** 

**Steg 2: [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Steg 3: [Skapa en Cognitive Services resurs](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Steg 4: [Kom igång med portalen](cognitive-search-quickstart-blob.md) eller – [Kom igång med exempel data med hjälp av rest och Postman](knowledge-store-howto.md)** 

Du kan använda rest `api-version=2019-05-06-Preview` för att skapa en AI-baserad pipeline som innehåller kunskaps lager. I det nyaste för hands versions-API: et `knowledgeStore` tillhandahåller färdigheter-objektet definitionen.

## <a name="takeaways"></a>Lärdomar

Kunskaps lager erbjuder en rad olika förmåner, inklusive men inte begränsat till att aktivera användning av de omfattande dokumenten i andra scenarier än Sök, kostnads kontroller och hantering av drift i din anriknings process. Dessa funktioner är allt tillgängliga för att enkelt använda genom att lägga till ett lagrings konto till din färdigheter och använda det uppdaterade uttrycks språket enligt beskrivningen i [så här kommer du igång med kunskaps lager](knowledge-store-howto.md). 

## <a name="next-steps"></a>Nästa steg

Den enklaste metoden för att skapa omfattande dokument är via guiden **Importera data** .

> [!div class="nextstepaction"]
> [Snabbstart: Prova kognitiv sökning i en portal genom gång](cognitive-search-quickstart-blob.md)
