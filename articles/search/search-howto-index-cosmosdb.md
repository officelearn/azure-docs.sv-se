---
title: Sök över Azure Cosmos DB-data
titleSuffix: Azure Cognitive Search
description: Importera data från Azure Cosmos DB till ett sökbart index i Azure Cognitive Search. Indexerare automatiserar datainmatning för valda datakällor som Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283009"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Så här indexerar du Cosmos DB-data med hjälp av en indexerare i Azure Cognitive Search 

> [!IMPORTANT] 
> SQL API är allmänt tillgängligt.
> MongoDB API, Gremlin API och Cassandra API-stöd finns för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Du kan begära åtkomst till förhandsgranskningarna genom att fylla [i det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

> [!WARNING]
> Endast Cosmos DB-samlingar med en [indexeringsprincip](https://docs.microsoft.com/azure/cosmos-db/index-policy) inställd [på Konsekvent](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) stöds av Azure Cognitive Search. Det rekommenderas inte att indexera samlingar med en lazy-indexeringsprincip och kan resultera i data som saknas. Samlingar med inaktiverad indexering stöds inte.

Den här artikeln visar hur du konfigurerar en Azure Cosmos [DB-indexerare](search-indexer-overview.md) för att extrahera innehåll och göra det sökbart i Azure Cognitive Search. Det här arbetsflödet skapar ett Azure Cognitive Search-index och läser in det med befintlig text som extraherats från Azure Cosmos DB. 

Eftersom terminologi kan vara förvirrande är det värt att notera att [Azure Cosmos DB-indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure Cognitive Search-indexering](search-what-is-an-index.md) är olika åtgärder som är unika för varje tjänst. Innan du startar Azure Cognitive Search-indexering måste din Azure Cosmos DB-databas redan finnas och innehålla data.

Cosmos DB-indexeraren i Azure Cognitive Search kan genomsöka [Azure Cosmos DB-objekt](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) som nås via olika protokoll. 

+ För [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), som är allmänt tillgängligt, kan du använda [portalen,](#cosmos-indexer-portal) [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) för att skapa datakällan och indexeraren.

+ För [MongoDB API (förhandsversion)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)kan du använda antingen [portalen](#cosmos-indexer-portal) eller [REST API-versionen 2019-05-06-Preview](search-api-preview.md) för att skapa datakällan och indexeraren.

+ För [Cassandra API (förhandsversion)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) och [Gremlin API (förhandsversion)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)kan du bara använda [REST API-versionen 2019-05-06-Preview](search-api-preview.md) för att skapa datakällan och indexeraren.


> [!Note]
> Du kan rösta på User Voice för [tabell-API:et](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) om du vill se det stöds i Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

> [!Note]
> Portalen stöder för närvarande SQL API och MongoDB API (preview).

Den enklaste metoden för att indexera Azure Cosmos DB-objekt är att använda en guide i [Azure-portalen](https://portal.azure.com/). Genom att samplingsdata och läsa metadata i behållaren kan guiden [**Importera data**](search-import-data-portal.md) i Azure Cognitive Search skapa ett standardindex, mappa källfält för att rikta indexfält och läsa in indexet i en enda åtgärd. Beroende på källdatas storlek och komplexitet kan du ha ett fungerande fulltextsökindex på några minuter.

Vi rekommenderar att du använder samma region eller plats för både Azure Cognitive Search och Azure Cosmos DB för lägre svarstid och för att undvika bandbreddsavgifter.

### <a name="1---prepare-source-data"></a>1 - Förbereda källdata

Du bör ha ett Cosmos DB-konto, en Azure Cosmos DB-databas mappad till SQL API, MongoDB API (förhandsversion) eller Gremlin API (förhandsversion) och innehåll i databasen.

Kontrollera att Cosmos DB-databasen innehåller data. [Guiden Importera data](search-import-data-portal.md) läser metadata och utför dataprovtagning för att dra slutsatsen ett indexschema, men den läser också in data från Cosmos DB. Om data saknas, slutar guiden med det här felet "Fel att identifiera indexschema från datakälla: Det gick inte att skapa en prototyp index eftersom datasource 'emptycollection' returnerade inga data".

### <a name="2---start-import-data-wizard"></a>2 - Guiden Starta importera data

Du kan [starta guiden](search-import-data-portal.md) från kommandofältet på tjänstsidan för Azure Cognitive Search, eller om du ansluter till Cosmos DB SQL API kan du klicka på Lägg till Azure **Cognitive Search** i avsnittet **Inställningar** i det vänstra navigeringsfönstret för ditt Cosmos DB-konto.

   ![Kommandot Importera data i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 - Ställ in datakällan

På **sidan datakälla** måste källan vara **Cosmos DB**med följande specifikationer:

+ **Namn** är namnet på datakällobjektet. När du har skapat det kan du välja det för andra arbetsbelastningar.

+ **Cosmos DB-konto** ska vara den primära eller sekundära `AccountEndpoint` anslutningssträngen från Cosmos DB, med en och en `AccountKey`. För MongoDB-samlingar lägger du till **ApiKind=MongoDb** i slutet av anslutningssträngen och separerar den från anslutningssträngen med ett semikolon. För Gremlin API och Cassandra API använder du instruktionerna för [REST API](#cosmosdb-indexer-rest).

+ **Databasen** är en befintlig databas från kontot. 

+ **Samlingen** är en behållare med dokument. Dokument måste finnas för att importen ska lyckas. 

+ **Frågan** kan vara tom om du vill ha alla dokument, annars kan du mata in en fråga som markerar en dokumentdelmängd. **Frågan** är endast tillgänglig för SQL API.

   ![Definition av datakälla för Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definition av datakälla för Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Hoppa över sidan "Berika innehåll" i guiden

Att lägga till kognitiva färdigheter (eller anrikning) är inte ett importkrav. Om du inte har ett specifikt behov av att lägga till [AI-anrikning](cognitive-search-concept-intro.md) i din indexeringspipeline bör du hoppa över det här steget.

Om du vill hoppa över steget klickar du på de blå knapparna längst ned på sidan för "Nästa" och "Hoppa över".

### <a name="5---set-index-attributes"></a>5 - Ange indexattribut

På sidan **Index** bör du se en lista över fält med en datatyp och en serie kryssrutor för inställning av indexattribut. Guiden kan generera en fältlista baserad på metadata och genom att prova källdata. 

Du kan mass markera attribut genom att klicka på kryssrutan högst upp i en attributkolumn. Välj **Hämtningsbar** och **sökbar** för varje fält som ska returneras till en klientapp och vara föremål för fulltextsökningsbearbetning. Du kommer att märka att heltal inte är fulltext eller suddiga sökbara (tal utvärderas ordagrant och är ofta användbara i filter).

Läs beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språkanalysatorer](https://docs.microsoft.com/rest/api/searchservice/language-support) för mer information. 

Ta en stund att granska dina val. När du har kört guiden skapas fysiska datastrukturer och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Definition av index för Kosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definition av index för Kosmos DB")

### <a name="6---create-indexer"></a>6 - Skapa indexerare

Guiden är helt angiven och skapar tre olika objekt i söktjänsten. Ett datakällobjekt och indexobjekt sparas som namngivna resurser i azure Cognitive Search-tjänsten. Det sista steget skapar ett indexerarobjekt. Genom att namnge indexeraren kan den finnas som en fristående resurs, som du kan schemalägga och hantera oberoende av index- och datakällobjektet, som skapas i samma guidesekvens.

Om du inte är bekant med indexerare är en *indexerare* en resurs i Azure Cognitive Search som genomsöker en extern datakälla efter sökbart innehåll. Utdataguiden **Import data** är en indexerare som genomsöker din Cosmos DB-datakälla, extraherar sökbart innehåll och importerar det till ett index på Azure Cognitive Search.

Följande skärmbild visar standardindexeringskonfigurationen. Du kan växla till **En** gång om du vill köra indexeraren en gång. Klicka på **Skicka** om du vill köra guiden och skapa alla objekt. Indexeringen påbörjas omedelbart.

   ![Definition av Indexeringsdefinition för Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definition av Indexeringsdefinition för Cosmos DB")

Du kan övervaka dataimporten på portalsidorna. Förloppsmeddelanden anger indexeringsstatus och hur många dokument som överförs. 

När indexeringen är klar kan du använda [Sökutforskaren](search-explorer.md) för att fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig kan du behöva ange fler attribut i fler fält. Ta bort index och indexerare som du just skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera Azure Cosmos DB-data, efter ett tredeladt arbetsflöde som är gemensamt för alla indexerare i Azure Cognitive Search: skapa en datakälla, skapa ett index, skapa en indexerare. Dataextrahering från Cosmos DB inträffar när du skickar begäran Skapa indexerare. När begäran är klar har du ett frågebart index. 

> [!NOTE]
> För indexering av data från Cosmos DB Gremlin API eller Cosmos DB Cassandra API måste du först begära åtkomst till de gated förhandsvisningarna genom att fylla [i det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). När din begäran har behandlats får du instruktioner för hur du använder [REST API-versionen 2019-05-06-Preview](search-api-preview.md) för att skapa datakällan.

Tidigare i den här artikeln nämns att [Azure Cosmos DB indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure Cognitive Search indexering indexering](search-what-is-an-index.md) är olika åtgärder. För Cosmos DB-indexering indexeras som standard alla dokument automatiskt utom med Cassandra API. Om du inaktiverar automatisk indexering kan dokument endast nås via sina självlänkar eller av frågor med hjälp av dokument-ID. Azure Cognitive Search-indexering kräver att Cosmos DB automatisk indexering aktiveras i samlingen som indexeras av Azure Cognitive Search. När du registrerar dig för förhandsversionen av Cosmos DB Cassandra API indexer får du instruktioner om hur du ställer in Cosmos DB-indexering.

> [!WARNING]
> Azure Cosmos DB är nästa generation av DocumentDB. Tidigare med API-version **2017-11-11** kan du använda syntaxen. `documentdb` Detta innebar att du kunde ange `cosmosdb` `documentdb`din datakälltyp som eller . Från och med API-version **2019-05-06** stöder både Azure `cosmosdb` Cognitive Search API:er och Portal endast syntaxen enligt instruktionerna i den här artikeln. Det innebär att datakälltypen måste `cosmosdb` om du vill ansluta till en Cosmos DB-slutpunkt.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montera ingångar för begäran

För varje begäran måste du ange tjänstnamn och administratörsnyckel för Azure Cognitive Search (i POST-huvudet) och lagringskontots namn och nyckel för blob-lagring. Du kan använda [Postman](search-get-started-postman.md) för att skicka HTTP-begäranden till Azure Cognitive Search.

Kopiera följande fyra värden till Anteckningar så att du kan klistra in dem i en begäran:

+ Namn på Azure Cognitive Search-tjänst
+ Administratörsnyckel för Azure Cognitive Search
+ Cosmos DB-anslutningssträng

Du hittar dessa värden i portalen:

1. Kopiera söktjänstens URL på portalsidorna för Azure Cognitive Search från sidan Översikt.

2. I det vänstra navigeringsfönstret klickar du på **Nycklar** och kopierar antingen primär- eller sekundärnyckeln (de är likvärdiga).

3. Växla till portalsidorna för ditt Cosmos-lagringskonto. Klicka på **Tangenter**under **Inställningar**i det vänstra navigeringsfönstret. Den här sidan innehåller en URI, två uppsättningar anslutningssträngar och två uppsättningar nycklar. Kopiera en av anslutningssträngarna till Anteckningar.

### <a name="2---create-a-data-source"></a>2 - Skapa en datakälla

En **datakälla** anger vilka data som ska indexeras, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i samlingen). Datakällan definieras som en oberoende resurs så att den kan användas av flera indexerare.

Om du vill skapa en datakälla formulerar du en POST-begäran:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Brödtexten i begäran innehåller definitionen av datakällan, som bör innehålla följande fält:

| Field   | Beskrivning |
|---------|-------------|
| **Namn** | Krävs. Välj ett namn som ska representera datakällobjektet. |
|**Typ**| Krävs. Måste `cosmosdb`vara . |
|**Autentiseringsuppgifter** | Krävs. Måste vara en Cosmos DB-anslutningssträng.<br/>För SQL-samlingar är anslutningssträngar i det här formatet:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>För MongoDB-samlingar lägger du till **ApiKind=MongoDb** i anslutningssträngen:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>För Gremlin-grafer och Cassandra-tabeller registrerar du dig för [förhandsgranskningen av gated indexeraren](https://aka.ms/azure-cognitive-search/indexer-preview) för att få tillgång till förhandsgranskningen och information om hur du formaterar autentiseringsuppgifterna.<br/><br/>Undvik portnummer i slutpunktsadressen. Om du inkluderar portnumret kan Azure Cognitive Search inte indexera din Azure Cosmos DB-databas.|
| **Behållare** | Innehåller följande element: <br/>**namn**: Obligatoriskt. Ange ID:t för den databassamling som ska indexeras.<br/>**fråga**: Valfritt. Du kan ange en fråga om du vill förenkla ett godtyckligt JSON-dokument till ett platt schema som Azure Cognitive Search kan indexera.<br/>För MongoDB API, Gremlin API och Cassandra API stöds inte frågor. |
| **dataChangeDetectionPolicy** | Rekommenderas. Se Avsnittet [Indexera ändrade dokument.](#DataChangeDetectionPolicy)|
|**dataUtetionDetectionPolicy** | Valfri. Se Avsnittet [Indexera borttagna dokument.](#DataDeletionDetectionPolicy)|

### <a name="using-queries-to-shape-indexed-data"></a>Använda frågor för att forma indexerade data
Du kan ange en SQL-fråga om du vill förenkla kapslade egenskaper eller matriser, projicera JSON-egenskaper och filtrera de data som ska indexeras. 

> [!WARNING]
> Anpassade frågor stöds inte för **MongoDB API**, **Gremlin API**och **Cassandra API**: `container.query` parametern måste anges till null eller utelämnas. Om du behöver använda en anpassad fråga, vänligen meddela oss på [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Exempeldokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filterfråga:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Förenklingsfråga:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projektionsfråga:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Fråga om förenkling av matris:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Skapa ett målsökindex 

[Skapa ett mål Azure Cognitive Search index](/rest/api/searchservice/create-index) om du inte redan har ett. I följande exempel skapas ett index med ett ID- och beskrivningsfält:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

Kontrollera att schemat för ditt målindex är kompatibelt med schemat för källanS JSON-dokument eller utdata från din anpassade frågeprojektion.

> [!NOTE]
> För partitionerade samlingar är standarddokumentnyckeln Azure `_rid` Cosmos DB:s egenskap, som `rid` Azure Cognitive Search automatiskt byter namn till eftersom fältnamn inte kan börja med ett understreck. Azure Cosmos DB-värden `_rid` innehåller också tecken som är ogiltiga i Azure Cognitive Search-nycklar. Därför är `_rid` värdena Base64 kodade.
> 
> För MongoDB-samlingar byter Azure Cognitive `_id` Search `id`automatiskt namn på egenskapen till .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mappning mellan JSON-datatyper och Azure Cognitive Search Data Types
| JSON-datatyp | Kompatibla målindexfälttyper |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Tal som ser ut som flyttan |Edm.Double, Edm.String |
| String |Edm.String |
| Matriser av primitiva typer, till exempel ["a", "b", "c"] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset, Edm.String |
| GeoJSON-objekt, till exempel { "typ": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Andra JSON-objekt |Ej tillämpligt |

### <a name="4---configure-and-run-the-indexer"></a>4 - Konfigurera och kör indexeraren

När indexet och datakällan har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Den här indexeraren körs varannan timme (schemaintervallet är inställt på "PT2H"). Om du vill köra en indexerare var 30:e minut ställer du in intervallet på "PT30M". Det kortaste intervallet som stöds är 5 minuter. Schemat är valfritt - om det utelämnas körs en indexerare bara en gång när det skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API:et skapa indexerare finns i [Skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexeringsscheman finns i [Så här schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Använda .NET

The generally available .NET SDK has full parity with the generally available REST API. Vi rekommenderar att du granskar det tidigare REST API-avsnittet för att lära dig begrepp, arbetsflöde och krav. Du kan sedan referera till följande .NET API-referensdokumentation för att implementera en JSON-indexerare i hanterad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexera ändrade dokument

Syftet med en princip för identifiering av dataändring är att effektivt identifiera ändrade dataobjekt. För närvarande är den [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) enda `_ts` principen som stöds egenskapen med hjälp av (tidsstämpel) som tillhandahålls av Azure Cosmos DB, som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Att använda den här principen rekommenderas starkt för att säkerställa goda indexerares prestanda. 

Om du använder en anpassad fråga `_ts` kontrollerar du att egenskapen projiceras av frågan.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Inkrementella förlopp och anpassade frågor

Inkrementell förlopp under indexeringen säkerställer att om indexeringskörningen avbryts av tillfälliga fel eller körningstidsgräns, kan indexeraren fortsätta där den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när stora samlingar indexeras. 

Om du vill aktivera inkrementella förlopp när du `_ts` använder en anpassad fråga kontrollerar du att frågan beställer resultatet efter kolumnen. Detta möjliggör periodisk kontrollpunkt som Azure Cognitive Search använder för att tillhandahålla inkrementella framsteg i närvaro av fel.   

I vissa fall, även om `ORDER BY [collection alias]._ts` frågan innehåller en sats, kan det hända `_ts`att Azure Cognitive Search inte drar slutsatsen att frågan är ordnad av . Du kan berätta för Azure Cognitive Search `assumeOrderByHighWaterMarkColumn` att resultaten ordnas med hjälp av konfigurationsegenskapen. Om du vill ange det här tipset skapar eller uppdaterar du indexeraren på följande sätt: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexera borttagna dokument

När rader tas bort från samlingen vill du normalt ta bort dessa rader från sökindexet också. Syftet med en princip för identifiering av databorttagning är att effektivt identifiera borttagna dataobjekt. För närvarande är den `Soft Delete` enda principen som stöds principen (borttagningen är markerad med en flagga av något slag), vilket anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Om du använder en anpassad fråga kontrollerar du `softDeleteColumnName` att egenskapen som refereras av projiceras av frågan.

I följande exempel skapas en datakälla med en princip för mjuk borttagning:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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

## <a name="next-steps"></a><a name="NextSteps"></a>Nästa steg

Grattis! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Cognitive Search med hjälp av en indexerare.

* Mer information om Azure Cosmos DB finns på [tjänstsidan för Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Mer information om Azure Cognitive Search finns på [sidan Söktjänst](https://azure.microsoft.com/services/search/).
