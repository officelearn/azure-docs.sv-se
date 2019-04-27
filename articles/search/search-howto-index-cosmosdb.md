---
title: Index-datakälla för ett Azure Cosmos DB – Azure Search
description: Crawla en Azure Cosmos DB-datakälla och mata in data i ett sökbart fulltextindex i Azure Search. Indexerare automatisera datainmatning för valda datakällor som Azure Cosmos DB.
ms.date: 02/28/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 019945c48342238a1caa7611bdff6d06fd1e2bd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871731"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Indexera Cosmos DB med en Azure Search-indexerare

Den här artikeln visar hur du konfigurerar ett Azure Cosmos DB [indexeraren](search-indexer-overview.md) att extrahera innehåll och gör det sökbara i Azure Search. Det här arbetsflödet skapar ett Azure Search-index och läser in den med befintliga text som extraherats från Azure Cosmos DB. 

Eftersom terminologi kan vara förvirrande, är det värt som [Azure Cosmos DB indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure sökindexering](search-what-is-an-index.md) är olika åtgärder, som är unika för varje tjänst. Innan du startar Azure Search måste indexering, Azure Cosmos DB-databasen redan finnas och innehålla data.

Du kan använda den [portal](#cosmos-indexer-portal), REST API: er eller .NET SDK för att indexera Cosmos-innehåll. Cosmos DB-indexeraren i Azure Search kan crawla [Azure Cosmos-objekt](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) nås via dessa protokoll:

* [SQL-API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB API](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) (Azure Search-stöd för detta API är allmänt tillgänglig förhandsversion)  

> [!Note]
> User Voice har befintliga objekt för ytterligare API-stöd. Du kan omvandla en röst för API: er för Cosmos som du skulle vilja se stöds i Azure Search: [Tabell-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera Azure Cosmos-objekt är att använda en guide i den [Azure-portalen](https://portal.azure.com/). Genom att sampla data och läsa metadata för behållaren i [ **dataimport** ](search-import-data-portal.md) guiden i Azure Search kan skapa ett Standardindex mappar källfält till målfälten för index och läsa in index i en enda åtgärden. Du kan ha ett operational fulltext search-index på några minuter beroende på storleken och komplexiteten i källdata.

Vi rekommenderar att du använder samma Azure-prenumeration för Azure Search och Azure Cosmos DB, helst i samma region.

### <a name="1---prepare-source-data"></a>1 – förbereda källdata

Du bör ha ett Cosmos-konto, en Azure Cosmos-databas som är mappad till SQL API eller MongoDB API och en behållare för JSON-dokument. 

Kontrollera att Cosmos DB-databasen innehåller data. Den [guiden Importera data](search-import-data-portal.md) läser metadata och utför av datasampling att härleda ett indexschema, men även belastningar data från Cosmos DB. Om data saknas i guiden slutar med det här felet ”fel upptäcka indexschema från datakällan: Det gick inte att skapa ett prototypindex eftersom datakällan 'emptycollection' returnerade inga data ”.

### <a name="2---start-import-data-wizard"></a>2 – starta guiden Importera data

Du kan [starta guiden](search-import-data-portal.md) från kommandofältet på sidan för Azure Search-tjänsten eller genom att klicka på **Lägg till Azure Search** i den **inställningar** vänstra delen av ditt storage-konto navigeringsfönstret.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 – Konfigurera datakällan

> [!NOTE] 
> För närvarande kan du kan inte skapa eller redigera **MongoDB** datakällor med hjälp av Azure portal eller .NET SDK. Men du **kan** övervaka körningshistorik för MongoDB-indexerare på portalen.

I den **datakälla** sidan måste vara **Cosmos DB**, med följande specifikationer:

+ **Namn på** är namnet på datakällobjektet. När du skapat kan du välja den för andra arbetsbelastningar.

+ **Cosmos DB-konto** ska vara primär eller sekundär anslutningssträng från Cosmos DB med en `AccountEndpoint` och en `AccountKey`. Konton som anger om data är konvertera som SQL API eller Mongo DB API

+ **Databasen** är en befintlig databas från kontot. 

+ **Samlingen** är en behållare med dokument. Dokument måste finnas för att importen ska lyckas. 

+ **Fråga** kan vara tom om du vill att alla dokument, annars du kan ange en fråga som väljer en delmängd av dokument. 

   ![Cosmos DB-datakälla definition](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "definition för Cosmos DB-datakälla")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – hoppa över ”Lägg till cognitive search”-sidan i guiden

Att lägga till kognitiva funktioner är inte nödvändigt för import av dokumentet. Om du inte har ett specifikt behov av att [innehåller API: er med Cognitive Services och transformeringar](cognitive-search-concept-intro.md) till din pipeline för fulltextindexering, bör du hoppa över det här steget.

Om du vill hoppa över steg du först gå till nästa sida.

   ![Knappen Nästa sida för kognitiv sökning](media/search-get-started-portal/next-button-add-cog-search.png)

Från sidan Gå du vidare till indexet anpassning.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – ange indexattribut

I den **Index** bör du se en lista med fält med datatypen och en serie kryssrutorna för att ange indexattribut. Guiden kan skapa en fält-lista baserat på metadata och av sampling källdata. 

Du kan bulk-Välj attribut genom att klicka på kryssrutan högst upp i en attributkolumn. Välj **hämtningsbara** och **sökbar** för varje fält som ska returneras till en klientapp och föremål för fulltext search bearbetning. Lägg märke till att heltal inte är fulltext eller fuzzy sökbara (siffror utvärderas ordagrant och är ofta användbara i filter).

Granska beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) för mer information. 

Ta en stund att granska dina val. När du kör guiden fysiska datastrukturer skapas och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Cosmos DB indexerar definition](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB indexdefinitionen")

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt anges, skapas tre olika objekt i din söktjänst. Ett datakällobjekt och index objekt sparas som namngivna resurser i Azure Search-tjänsten. Det sista steget skapar ett indexer-objekt. Namngivning av indexeraren kan det finnas som en fristående-resurs som du kan schemalägga och hantera oberoende av index och källobjektet, skapas i samma följd i guiden.

Om du inte är bekant med indexerare, en *indexeraren* är en resurs i Azure Search som crawlar en extern datakälla för sökbart innehåll. Utdata från den **dataimport** guiden är en indexerare som crawlar din Cosmos DB-datakälla, den extraherar sökbart innehåll och importerar den till ett index i Azure Search.

I följande skärmbild visas indexeraren standardkonfigurationen. Du kan växla till **när** om du vill köra indexeraren en gång. Klicka på **skicka** att köra guiden och skapa alla objekt. Indexering inleds omedelbart.

   ![Cosmos DB indexerarens definition](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexerarens definition")

Du kan övervaka dataimporten i portalens sidor. Förlopp meddelanden visar status för indexering och hur många dokument laddas upp. 

När indexering är klar kan du använda [Sökutforskaren](search-explorer.md) fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig, kan du behöva ange fler attribut på flera fält. Ta bort index och indexerare som du just skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera Azure Cosmos DB data efter ett arbetsflöde för tre delar som är gemensamma för alla indexerare i Azure Search: skapa en datakälla, skapa ett index, skapa en indexerare. Extrahering av data från Cosmos storage inträffar när du skickar in begäran skapa et indexerare. När den här begäran har slutförts har du ett frågningsbart index. 

Om du utvärderar MongoDB, måste du använda REST API för att skapa datakällan.

Du kan välja om du vill att samlingen som automatiskt indexerar alla dokument i ditt Cosmos DB-konto. Alla dokument som indexeras automatiskt som standard, men du kan inaktivera automatisk indexering. När är avstängd indexera dokument kan nås endast via sina egna länkar eller av frågor med hjälp av webbplatsen-ID. Azure Search kräver Cosmos DB automatisk indexering för att aktiveras i den samling som kommer att indexeras av Azure Search. 

> [!NOTE]
> Azure Cosmos DB är nästa generation av DocumentDB. Även om produktens namn ändras den `documentdb` syntax i Azure Search-indexerare fortfarande finns för bakåtkompatibilitet kompatibilitet i både Azure Search API: er och sidor. När du konfigurerar indexerare, måste du ange den `documentdb` syntax enligt anvisningarna i den här artikeln.


### <a name="1---assemble-inputs-for-the-request"></a>1 – Assemblera indata för begäran

För varje begäran måste du ange namnet på tjänsten och admin-nyckel för Azure Search (i rubriken POST) och lagringskontonamn och nyckel för blob storage. Du kan använda [Postman](search-fiddler.md) att skicka HTTP-begäranden till Azure Search.

Kopiera följande fyra värden till anteckningar så att du kan klistra in dem i en begäran:

+ Azure Search-tjänstnamn
+ Azure Search-administratörsnyckel
+ Cosmos DB-anslutningssträng

Du hittar dessa värden i portalen:

1. Kopiera URL: en för search-tjänsten från sidan översikt på portalsidor för Azure Search.

2. I det vänstra navigeringsfönstret klickar du på **nycklar** och kopierar sedan antingen den primära eller sekundära nyckeln (de är likvärdiga).

3. Växla till portalsidor för ditt Cosmos-lagringskonto. I det vänstra navigeringsfönstret under **inställningar**, klickar du på **nycklar**. Den här sidan innehåller en URI, två uppsättningar anslutningssträngar, och två uppsättningar nycklar. Kopiera en av anslutningssträngar till anteckningar.

### <a name="2---create-a-data-source"></a>2 – Skapa en datakälla

En **datakälla** anger data till indexet, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i din samling). Datakällan har definierats som en oberoende resurs så att den kan användas av flera indexerare.

Formulera en POST-begäran för att skapa en datakälla:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Brödtexten i begäran innehåller definitionen av datakällan, vilket bör innehålla följande fält:

| Fält   | Beskrivning |
|---------|-------------|
| **Namn** | Krävs. Välj ett namn som representerar din datakällobjektet. |
|**typ**| Krävs. Måste vara `documentdb`. |
|**Autentiseringsuppgifter** | Krävs. Måste vara en Cosmos DB-anslutningssträng.<br/>För SQL-samlingar finns anslutningssträngar i följande format: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>För MongoDB-samlingar, lägger du till **ApiKind = MongoDb** på anslutningssträngen:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Undvik att portnumren i slutpunkts-url. Om du inkluderar portnumret går Azure Search inte att indexera Azure Cosmos DB-databasen.|
| **container** | Innehåller följande element: <br/>**name**: Krävs. Ange ID för samlingen databas som ska indexeras.<br/>**fråga**: Valfri. Du kan ange en fråga för att platta ut en godtycklig JSON-dokumentet till ett fast schema som Azure Search kan indexera.<br/>Frågor stöds inte för MongoDB-samlingar. |
| **dataChangeDetectionPolicy** | Vi rekommenderar. Se [indexering ändrats dokument](#DataChangeDetectionPolicy) avsnittet.|
|**dataDeletionDetectionPolicy** | Valfri. Se [indexering bort dokument](#DataDeletionDetectionPolicy) avsnittet.|

### <a name="using-queries-to-shape-indexed-data"></a>Med hjälp av frågor för att forma indexerat data
Du kan ange en SQL-fråga för att platta ut kapslade egenskaper eller matriser, projekt JSON-egenskaper och filtrera data som ska indexeras. 

> [!WARNING]
> Anpassade frågor stöds inte för **MongoDB** samlingar: `container.query` parametern måste anges till null eller utelämnas. Om du vill använda en anpassad fråga kan kontakta oss gärna på [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Exempel dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Filterfråga:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Förenkla frågan:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projektion av fråga:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Matris förenkling fråga:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 – skapa en målsökindex 

[Skapa ett Azure Search målindex](/rest/api/searchservice/create-index) om du inte redan har en. I följande exempel skapas ett index med ett ID och en beskrivning fält:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Kontrollera att schemat för din målindex är kompatibel med schemat för käll-JSON-dokument eller utdata för anpassad fråga-projektion.

> [!NOTE]
> Partitionerade samlingar dokumentnyckeln som standard är Azure Cosmos DB `_rid` egenskapen, som Azure Search kan automatiskt byter namn till `rid` eftersom fältnamn inte får börja med ett undescore tecken. Dessutom Azure Cosmos DB `_rid` värden innehåller tecken som är ogiltiga i Azure Search-nycklar. Därför måste den `_rid` värden är Base64-kodad.
> 
> För MongoDB samlingar Azure Search automatiskt byter namn på den `_id` egenskap `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappningen mellan JSON-datatyper och Azure Search-datatyper
| JSON-datatypen | Kompatibla target index fälttyper |
| --- | --- |
| Booleskt |Edm.Boolean, Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Nummer att ut flytande punkter |Edm.Double, Edm.String |
| String |Edm.String |
| Matriser av primitiva typer, till exempel [”a”, ”b”, ”c”] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset, Edm.String |
| GeoJSON-objekt, till exempel {”type”: "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Andra JSON-objekt |Gäller inte |

### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurera och köra indexeraren

När index och datakälla har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren körs varannan timme (schemaintervallet är inställd på ”PT2H”). Ange intervallet till ”PT30M” för att köra en indexerare var 30: e minut. Den kortaste stöds är 5 minuter. Schemat är valfritt – om det utelämnas, en indexerare körs en gång när den har skapats. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API: et för skapa indexerare finns [skapa et indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="use-net"></a>Använda .NET

.NET SDK har fullständigt paritet med REST API. Vi rekommenderar att du läser avsnittet ovan REST API för att lära dig begrepp, arbetsflöde och krav. Du kan referera till följande .NET API-referensdokumentation att implementera en JSON-indexerare i förvaltad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexera ändrade dokument

Syftet med en princip för data ändringen är att effektivt identifiera ändrade dataobjekt. Den enda stödda principen är för närvarande den `High Water Mark` genom att använda den `_ts` () tidsstämpelsegenskapen tillhandahålls av Azure Cosmos DB, som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Med den här principen rekommenderar starkt att säkerställa bra indexeraren prestanda. 

Om du använder en anpassad fråga, se till att den `_ts` egenskapen projiceras av frågan.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Stegvisa framsteg och anpassade frågor

Stegvisa framsteg under indexering säkerställer att om indexerare körningen har avbrutits av tillfälliga fel eller tidsgräns för körning kan välja indexeraren där den avbröts nästa gång den körs, i stället för att att indexera hela samlingen från grunden. Detta är särskilt viktigt vid indexering av stora samlingar. 

Aktivera stegvisa framsteg när du använder en anpassad fråga genom att se till att din fråga beställningar resultaten efter den `_ts` kolumn. På så sätt kan du periodiska kontrollpunkter som använder Azure Search för att tillhandahålla stegvisa framsteg om det förekommer fel.   

I vissa fall, även om frågan innehåller en `ORDER BY [collection alias]._ts` -satsen, Azure Search kan inte att härleda att frågan är sorterade efter den `_ts`. Du kan se Azure Search att resultaten sorteras med hjälp av den `assumeOrderByHighWaterMarkColumn` konfigurationsegenskapen. Om du vill ange den här tipset, skapa eller uppdatera indexeraren enligt följande: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexering bort dokument

När rader har tagits bort från samlingen, du normalt ta bort de raderna från search-index. Syftet med en princip för borttagning av data är att effektivt identifiera borttagna dataobjekt. Den enda stödda principen är för närvarande den `Soft Delete` princip (borttagning markeras med en flagga av något slag), som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Om du använder en anpassad fråga, se till att egenskapen refereras av `softDeleteColumnName` projiceras av frågan.

I följande exempel skapas en datakälla med en princip för mjuk borttagning:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="watch-this-video"></a>Titta på den här videon

I den här något äldre 7-videon visar Azure Cosmos DB-Programhanteraren Andrew Liu hur du lägger till ett Azure Search-index till en Azure Cosmos DB-behållare. Portalens sidor visas i videon är inaktuell, men informationen gäller fortfarande.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

## <a name="NextSteps"></a>Nästa steg

Grattis! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Search med en indexerare.

* Läs mer om Azure Cosmos DB i den [service-sidan för Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Läs mer om Azure Search i den [söktjänstsidan](https://azure.microsoft.com/services/search/).
