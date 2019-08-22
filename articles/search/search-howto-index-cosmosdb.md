---
title: Indexera en Azure Cosmos DB data källa – Azure Search
description: Crawla en Azure Cosmos DB data källa och mata in data i ett fullständigt text sökbart index i Azure Search. Indexerare automatiserar data inmatning för valda data källor som Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656699"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Indexera Cosmos DB med hjälp av en Azure Search-indexerare


> [!Note]
> Stöd för MongoDB-API är i för hands version och är inte avsett för användning i produktion. Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.
>
> SQL API är allmänt tillgängligt.

Den här artikeln visar hur du konfigurerar en Azure Cosmos DB [indexerare](search-indexer-overview.md) för att extrahera innehåll och göra den sökbar i Azure Search. Det här arbets flödet skapar ett Azure Search-index och läser in det med befintlig text som extraherats från Azure Cosmos DB. 

Eftersom terminologin kan vara förvirrande, är det värt att notera att [Azure Cosmos DB indexering](https://docs.microsoft.com/azure/cosmos-db/index-overview) och [Azure Search indexering](search-what-is-an-index.md) är distinkta åtgärder som är unika för varje tjänst. Innan du börjar Azure Search indexering måste Azure Cosmos DB databasen redan finnas och innehålla data.

Du kan använda [portalen](#cosmos-indexer-portal), REST-API: er eller .NET SDK för att indexera Cosmos-innehåll. Cosmos DB indexeraren i Azure Search kan crawla [Azure Cosmos-objekt](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) som nås via dessa protokoll:

* [SQL-API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB-API (för hands version)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> Användar rösten har befintliga objekt för ytterligare API-stöd. Du kan omvandla en röst för Cosmos-API: er som du vill se stöd för i Azure Search: [Tabell-API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4) [API för Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Använda portalen

Den enklaste metoden för att indexera Azure Cosmos-objekt är att använda en guide i [Azure Portal](https://portal.azure.com/). Genom att sampla data och läsa metadata i behållaren kan guiden [**Importera data**](search-import-data-portal.md) i Azure Search skapa ett standard index, mappa käll fält till mål index fält och läsa in indexet i en enda åtgärd. Beroende på storleken och komplexiteten hos källdata kan du ha ett fullständigt texts öknings index på några minuter.

Vi rekommenderar att du använder samma Azure-prenumeration för både Azure Search och Azure Cosmos DB, helst i samma region.

### <a name="1---prepare-source-data"></a>1 – Förbered källdata

Du bör ha ett Cosmos-konto, en Azure Cosmos-databas som är mappad till SQL API eller MongoDB API och en behållare med JSON-dokument. 

Kontrol lera att Cosmos DB-databasen innehåller data. [Guiden Importera data](search-import-data-portal.md) läser metadata och utför data sampling för att härleda ett index schema, men läser även in data från Cosmos dB. Om data saknas slutar guiden med det här felet "fel vid identifiering av index schema från data Källa: Det gick inte att skapa ett prototyp index eftersom DataSource ' emptycollection ' returnerade inga data.

### <a name="2---start-import-data-wizard"></a>2-Starta guiden Importera data

Du kan [starta guiden](search-import-data-portal.md) från kommando fältet på sidan Azure Search tjänst eller genom att klicka på **Lägg till Azure Search** i avsnittet **Inställningar** i lagrings kontots vänstra navigerings fönster.

   ![Importera data kommando i portalen](./media/search-import-data-portal/import-data-cmd2.png "Starta guiden Importera data")

### <a name="3---set-the-data-source"></a>3 – ange data källan

> [!NOTE] 
> För närvarande kan du inte skapa eller redigera **MongoDB** -datakällor med Azure Portal eller .NET SDK. Du **kan** dock övervaka körnings historiken för MongoDB-indexerare i portalen.

På sidan **data källa** måste källan vara **Cosmos DB**, med följande specifikationer:

+ **Namn** är namnet på objektet i data källan. När du har skapat kan du välja den för andra arbets belastningar.

+ **Cosmos DB konto** ska vara den primära eller sekundära anslutnings strängen från Cosmos DB, med en `AccountEndpoint` och en `AccountKey`. Kontot bestämmer om data ska omvandlas som SQL API eller Mongo DB API

+ **Databasen** är en befintlig databas från kontot. 

+ **Samlingen** är en behållare med dokument. Det måste finnas dokument för att importen ska lyckas. 

+ **Frågan** kan vara tom om du vill ha alla dokument, annars kan du mata in en fråga som väljer en delmängd av dokumentet. 

   ![Cosmos DB definition av data källa](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definition av data källa")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – hoppa över sidan Lägg till kognitiv sökning i guiden

Det är inte nödvändigt att lägga till kognitiva kunskaper för dokument import. Om du inte har ett särskilt behov av att [inkludera API:er för Cognitive Services och omvandlingar](cognitive-search-concept-intro.md) till din indexerings pipeline, bör du hoppa över det här steget.

Om du vill hoppa över steget går du först till nästa sida.

   ![Knappen Nästa sida för kognitiv sökning](media/search-get-started-portal/next-button-add-cog-search.png)

Från den sidan kan du gå vidare till index anpassning.

   ![Hoppa över steget Kognitiva kunskaper](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5-Ange indexattribut

På sidan **index** visas en lista med fält med data typen och en serie kryss rutor för att ställa in indexattribut. Guiden kan generera en fält lista baserat på metadata och genom att sampla data källan. 

Du kan massredigera attribut genom att klicka på kryss rutan överst i en Attribute-kolumn. Välj **hämtnings** **Bart** och sökbart för varje fält som ska returneras till en klient-app och för full texts öknings bearbetning. Du märker att heltal inte är full text eller fuzzy sökbar (tal utvärderas orda Grant och ofta är användbara i filter).

Granska beskrivningen av [indexattribut](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) och [språk analys](https://docs.microsoft.com/rest/api/searchservice/language-support) verktyg för mer information. 

Ägna en stund åt att granska dina val. När du har kört guiden skapas fysiska data strukturer och du kan inte redigera dessa fält utan att släppa och återskapa alla objekt.

   ![Cosmos DB index definition](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definition")

### <a name="6---create-indexer"></a>6 – skapa indexerare

Fullständigt angivet skapar guiden tre distinkta objekt i din Sök tjänst. Ett data käll objekt och ett index objekt sparas som namngivna resurser i Azure Searchs tjänsten. Det sista steget skapar ett indexerare-objekt. Om du namnger indexeraren kan den finnas som en fristående resurs, som du kan schemalägga och hantera oberoende av indexet och datakällobjektet, vilket skapas i samma guide ordning.

Om du inte är bekant med indexerare är en *indexerare* en resurs i Azure Search som crawlar en extern data källa för sökbart innehåll. Utdata från guiden **Importera data** är en indexerare som crawlar din Cosmos db data källa, extraherar sökbart innehåll och importerar det till ett index på Azure Search.

Följande skärm bild visar standard indexerings konfigurationen. Du kan växla till **en gång** om du vill köra indexeraren en gång. Klicka på **Skicka** för att köra guiden och skapa alla objekt. Indexeringen börjar omedelbart.

   ![Cosmos DB indexerings definition](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexerings definition")

Du kan övervaka data import på Portal sidorna. Förlopps meddelanden indikerar indexerings status och hur många dokument som laddas upp. 

När indexeringen är klar kan du använda [Sök Utforskaren](search-explorer.md) för att fråga ditt index.

> [!NOTE]
> Om du inte ser de data du förväntar dig kan du behöva ange fler attribut för fler fält. Ta bort indexet och indexeraren som du nyss skapade och gå igenom guiden igen och ändra dina val för indexattribut i steg 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Använda REST-API:er

Du kan använda REST API för att indexera Azure Cosmos DB data, efter ett arbets flöde med tre delar som är gemensamt för alla indexerare i Azure Search: skapa en data källa, skapa ett index, skapa en indexerare. Data extrahering från Cosmos-lagring sker när du skickar en begäran om att skapa indexerare. När den här begäran har avslut ATS har du ett index som kan anropas. 

Om du utvärderar MongoDB måste du använda resten `api-version=2019-05-06-Preview` för att skapa data källan.

I ditt Cosmos DB-konto kan du välja om du vill att samlingen automatiskt ska indexera alla dokument. Som standard indexeras alla dokument automatiskt, men du kan inaktivera automatisk indexering. När indexeringen är inaktive rad kan dokument endast nås via sina egna länkar eller via frågor med hjälp av dokument-ID: t. Azure Search Cosmos DB kräver att automatisk indexering aktive ras i samlingen som ska indexeras av Azure Search. 

> [!WARNING]
> Azure Cosmos DB är nästa generation av DocumentDB. Tidigare med API version **2017-11-11** kan du använda `documentdb` syntaxen. Detta innebar att du kan ange typ av data källa `cosmosdb` som `documentdb`eller. Från och med API version **2019-05-06** har båda Azure Search API: er och portalen `cosmosdb` endast stöd för syntaxen enligt anvisningarna i den här artikeln. Det innebär att data källans typ måste `cosmosdb` anges om du vill ansluta till en Cosmos DB-slutpunkt.

### <a name="1---assemble-inputs-for-the-request"></a>1 – Sammanställ indata för begäran

För varje begäran måste du ange tjänst namnet och administratörs nyckeln för Azure Search (i POST huvudet) och lagrings kontots namn och nyckel för Blob Storage. Du kan använda [Postman](search-get-started-postman.md) för att skicka HTTP-begäranden till Azure Search.

Kopiera följande fyra värden till anteckningar så att du kan klistra in dem i en förfrågan:

+ Azure Search tjänstens namn
+ Azure Search administratörs nyckel
+ Cosmos DB anslutnings sträng

Du kan hitta dessa värden i portalen:

1. På Portal sidorna för Azure Search kopierar du URL: en för Sök tjänsten från översikts sidan.

2. I det vänstra navigerings fönstret klickar du på **nycklar** och kopierar antingen den primära eller sekundära nyckeln (de är motsvarande).

3. Växla till Portal sidorna för ditt Cosmos-lagrings konto. I det vänstra navigerings fönstret, under **Inställningar**, klickar du på **nycklar**. Den här sidan innehåller en URI, två uppsättningar anslutnings strängar och två uppsättningar nycklar. Kopiera en av anslutnings strängarna till anteckningar.

### <a name="2---create-a-data-source"></a>2 – Skapa en data Källa

En **data källa** anger de data som ska indexeras, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i din samling). Data källan definieras som en oberoende resurs så att den kan användas av flera indexerare.

Formulera en POST-begäran för att skapa en data Källa:

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

Bröd texten i begäran innehåller definitionen av data källan, som ska innehålla följande fält:

| Fält   | Beskrivning |
|---------|-------------|
| **name** | Obligatoriskt. Välj ett namn som ska representera ditt data käll objekt. |
|**type**| Obligatoriskt. Måste vara `cosmosdb`. |
|**klientautentiseringsuppgifter** | Obligatoriskt. Måste vara en Cosmos DB anslutnings sträng.<br/>För SQL-samlingar är anslutnings strängar i följande format:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>För MongoDB-samlingar lägger du till **ApiKind = MongoDB** i anslutnings strängen:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Undvik port nummer i slut punkts-URL: en. Om du inkluderar port numret kommer Azure Search inte att kunna indexera Azure Cosmos DB-databasen.|
| **fönster** | Innehåller följande element: <br/>**name**: Obligatoriskt. Ange ID för den databas samling som ska indexeras.<br/>**fråga**: Valfritt. Du kan ange en fråga för att förenkla ett godtyckligt JSON-dokument till ett plant schema som Azure Search kan indexera.<br/>Frågor stöds inte för MongoDB-samlingar. |
| **dataChangeDetectionPolicy** | Rekommenderas. Se avsnittet [Indexera ändrade dokument](#DataChangeDetectionPolicy) .|
|**dataDeletionDetectionPolicy** | Valfritt. Se avsnittet [Indexera borttagna dokument](#DataDeletionDetectionPolicy) .|

### <a name="using-queries-to-shape-indexed-data"></a>Använda frågor för att forma indexerade data
Du kan ange en SQL-fråga för att förenkla inkapslade egenskaper eller matriser, egenskaper för projekt-JSON och filtrera data som ska indexeras. 

> [!WARNING]
> Anpassade frågor stöds inte för **MongoDB** -samlingar: `container.query` parametern måste anges till null eller utelämnad. Om du behöver använda en anpassad fråga kan du berätta för oss om [användar rösten](https://feedback.azure.com/forums/263029-azure-search).

Exempel dokument:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filter fråga:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Förenklings fråga:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projektions fråga:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Fråga för mat ris förenkling:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 – skapa ett mål Sök index 

[Skapa ett mål Azure Search index](/rest/api/searchservice/create-index) om du inte redan har en. I följande exempel skapas ett index med ett ID och beskrivnings fält:

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

Se till att schemat för mål indexet är kompatibelt med schemat för käll-JSON-dokumenten eller utdata för din anpassade fråga-projektion.

> [!NOTE]
> För partitionerade samlingar är standard dokument nyckeln Azure Cosmos DB `_rid` egenskapen, som Azure Search automatiskt byter namn till `rid` eftersom fält namn inte får börja med ett undescore-värde. Azure Cosmos DB `_rid` värden innehåller också ogiltiga tecken i Azure Search nycklar. Därför är `_rid` värdena base64-kodade.
> 
> För MongoDB-samlingar byter Azure Search automatiskt namn på `_id` egenskapen till. `doc_id`  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappning mellan JSON-datatyper och Azure Search data typer
| Data typen JSON | Kompatibla fält typer för mål index |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Siffror som ser ut som svävande punkter |Edm.Double, Edm.String |
| Sträng |Edm.String |
| Matriser med primitiva typer, till exempel ["a", "b", "c"] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset, Edm.String |
| Subjson-objekt, till exempel {"typ": "Punkt", "koordinater": [Long, Lat]} |Edm.GeographyPoint |
| Andra JSON-objekt |Gäller inte |

### <a name="4---configure-and-run-the-indexer"></a>4 – Konfigurera och kör indexeraren

När indexet och data källan har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Indexeraren körs varannan timme (schema intervall anges till "PT2H"). Om du vill köra en indexerare var 30: e minut anger du intervallet till "PT30M". Det kortaste intervall som stöds är 5 minuter. Schemat är valfritt – om det utelämnas körs en indexerare bara en gång när den skapas. Du kan dock köra en indexerare på begäran när du vill.   

Mer information om API för att skapa index finns i [skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Mer information om hur du definierar indexerare scheman finns i [så här schemalägger du indexerare för Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Använda .NET

Den allmänt tillgängliga .NET SDK: n har fullständig paritet med allmänt tillgängliga REST API. Vi rekommenderar att du läser avsnittet tidigare REST API för att lära dig begrepp, arbets flöden och krav. Du kan sedan se följande dokumentation om .NET API-referens för att implementera en JSON-indexerare i förvaltad kod.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexera ändrade dokument

Syftet med en princip för data ändrings identifiering är att effektivt identifiera ändrade data objekt. För närvarande är den enda princip som stöds `High Water Mark` den princip som `_ts` använder egenskapen (timestamp) som tillhandahålls av Azure Cosmos DB, vilket anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Att använda den här principen rekommenderas starkt för att säkerställa prestanda för bästa indexerare. 

Om du använder en anpassad fråga ska du kontrol lera att `_ts` egenskapen har projicerats av frågan.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Stegvisa framsteg och anpassade frågor

Stegvis förlopp under indexeringen säkerställer att om körningen av Indexer avbryts vid tillfälliga haverier eller körnings tids gräns, kan indexeraren Hämta var den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när du indexerar stora samlingar. 

Om du vill aktivera stegvisa förloppet när du använder en anpassad fråga måste du se till att frågan `_ts` beställer resultatet efter kolumnen. Detta möjliggör regelbunden kontroll som pekar på Azure Search använder för att ge stegvisa framsteg i närvaro av problem.   

I vissa fall, även om frågan innehåller en `ORDER BY [collection alias]._ts` -sats, kan Azure Search inte härleda att frågan beställs `_ts`av. Du kan se Azure Search att resultatet sorteras med hjälp `assumeOrderByHighWaterMarkColumn` av konfigurations egenskapen. Om du vill ange det här tipset skapar eller uppdaterar du indexeraren enligt följande: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexera borttagna dokument

När rader tas bort från samlingen vill du normalt även ta bort dessa rader från Sök indexet. Syftet med en identifierings princip för data borttagning är att effektivt identifiera borttagna data objekt. För närvarande är `Soft Delete` den enda princip som stöds principen (borttagning är markerad med en flagga för viss sortering), som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Om du använder en anpassad fråga ser du till att den egenskap som har refererats `softDeleteColumnName` av är beräknad av frågan.

I följande exempel skapas en data källa med en princip för mjuk borttagning:

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

## <a name="NextSteps"></a>Nästa steg

Grattis! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Search med hjälp av en indexerare.

* Mer information om Azure Cosmos DB finns på sidan för [Azure Cosmos DB tjänsten](https://azure.microsoft.com/services/cosmos-db/).
* Mer information om Azure Search finns på [sidan Sök tjänst](https://azure.microsoft.com/services/search/).
