---
title: Indexera en Azure Cosmos DB-datakälla för Azure Search | Microsoft Docs
description: Den här artikeln visar hur du skapar en Azure Search-indexerare med en Azure Cosmos DB-datakälla.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.openlocfilehash: 07768ee1590fa087a1eb1486cb59ab0f57d02b64
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747549"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Ansluta Cosmos DB med Azure Search med indexerare

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Konfigurera [Azure Search-indexerare](search-indexer-overview.md) som använder en Azure Cosmos DB-samling som en datakälla.
> * Skapa ett sökindex med JSON-kompatibla datatyper.
> * Konfigurera en indexerare för på begäran och återkommande indexering.
> * Stegvis uppdatera index baserat på ändringar i underliggande data.

> [!NOTE]
> Azure Cosmos DB är nästa generation av DocumentDB. Även om produktens namn ändras den `documentdb` syntax i Azure Search-indexerare fortfarande finns för bakåtkompatibilitet kompatibilitet i både Azure Search API: er och sidor. När du konfigurerar indexerare, måste du ange den `documentdb` syntax enligt anvisningarna i den här artikeln.

I följande video visar Azure Cosmos DB-Programhanteraren Andrew Liu hur du lägger till ett Azure Search-index till en Azure Cosmos DB-behållare.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>API-typer som stöds

Även om Azure Cosmos DB stöder en mängd olika datamodeller och API: er, utökar produktionssupport för Azure Search-indexerare till SQL-API. Stöd för MongoDB API är för närvarande i offentlig förhandsversion.  

Stöd för ytterligare API: er är kommande. För att hjälpa oss att prioritera vilka som ska stödja först konvertera din röst på User Voice-webbplatsen:

* [Stöd för tabell-API datakällor](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Stöd för Graph API datakällor](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Stöd för Apache Cassandra API datakällor](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Förutsättningar

Förutom en Cosmos DB-konto, måste du ha en [Azure Search-tjänst](search-create-service-portal.md). 

Du kan välja om du vill att samlingen som automatiskt indexerar alla dokument i ditt Cosmos DB-konto. Alla dokument som indexeras automatiskt som standard, men du kan inaktivera automatisk indexering. När är avstängd indexera dokument kan nås endast via sina egna länkar eller av frågor med hjälp av webbplatsen-ID. Azure Search kräver Cosmos DB automatisk indexering för att aktiveras i den samling som kommer att indexeras av Azure Search. 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure Search-indexeraren begrepp

En **datakälla** anger data till indexet, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i din samling). Datakällan har definierats som en oberoende resurs så att den kan användas av flera indexerare.

En **indexeraren** beskriver hur data flödar från datakällan till ett mål search-index. En indexerare kan användas för att:

* Utföra en enstaka kopia av data kan fylla ett index.
* Synkronisera ett index med ändringar i datakällan enligt ett schema.
* Anropa uppdateringar på begäran till ett index efter behov.

Om du vill konfigurera ett Azure Cosmos DB-indexeraren måste du skapa ett index, datakälla och slutligen indexeraren. Du kan skapa dessa objekt med hjälp av den [portal](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search), eller [REST API](/rest/api/searchservice/). 

Den här artikeln visar hur du använder REST-API. Om du väljer för portalen, den [guiden Importera data](search-import-data-portal.md) vägleder dig genom skapandet av alla dessa resurser, inklusive indexet.

> [!TIP]
> Du kan starta guiden **Importera data** från Azure Cosmos DB-instrumentpanelen för att förenkla indexeringen för datakällan. Välj **Samlingar** > **Lägg till Azure Search** i navigeringsfältet till vänster för att komma igång.

> [!NOTE] 
> För tillfället kan inte skapa eller redigera **MongoDB** datakällor med hjälp av Azure Portal eller .NET SDK. Men du **kan** övervaka körningshistorik för MongoDB-indexerare på portalen.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
Gör ett INLÄGG om du vill skapa en datakälla:

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

* **namn på**: Välj ett valfritt namn som representerar din databas.
* **typ**: måste vara `documentdb`.
* **autentiseringsuppgifter**:
  
  * **connectionString**: krävs. Ange anslutningsinformationen till din Azure Cosmos DB-databas i följande format: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` för MongoDB-samlingar, lägga till **ApiKind = MongoDb** på anslutningssträngen: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  Undvik att portnumren i slutpunkts-url. Om du inkluderar portnumret går Azure Search inte att indexera Azure Cosmos DB-databasen.
* **behållaren**:
  
  * **namn på**: krävs. Ange id för samlingen databas som ska indexeras.
  * **fråga**: valfritt. Du kan ange en fråga för att platta ut en godtycklig JSON-dokumentet till ett fast schema som Azure Search kan indexera. Frågor stöds inte för MongoDB-samlingar. 
* **dataChangeDetectionPolicy**: rekommenderas. Se [indexering ändrats dokument](#DataChangeDetectionPolicy) avsnittet.
* **dataDeletionDetectionPolicy**: valfritt. Se [indexering bort dokument](#DataDeletionDetectionPolicy) avsnittet.

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

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Skapa en target Azure Search-index om du inte redan har en. Du kan skapa ett index med hjälp av den [Azure-portalens användargränssnitt](search-create-index-portal.md), [skapa Index REST API](/rest/api/searchservice/create-index) eller [Index klass](/dotnet/api/microsoft.azure.search.models.index).

I följande exempel skapas ett index med ett id och en beskrivning fält:

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
| Bool |Edm.Boolean Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Nummer att ut flytande punkter |Edm.Double Edm.String |
| Sträng |Edm.String |
| Matriser av primitiva typer, till exempel [”a”, ”b”, ”c”] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset Edm.String |
| GeoJSON-objekt, till exempel {”type”: ”Point”, ”coordinates”: [long, lat]} |Edm.GeographyPoint |
| Andra JSON-objekt |Gäller inte |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare

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

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Kör indexerare på begäran
Förutom att köra regelbundet enligt ett schema, kan du också anropa en indexerare på begäran:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> När du kör API: et returnerar har, indexeraren anrop har schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka statusen för indexerare i portalen eller med ett komma indexeraren Status API, vilket beskrivs härnäst. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Hämta status för indexerare
Du kan hämta status och körning historiken för en indexerare:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

Svaret innehåller status för övergripande indexerare, senaste (eller pågående) indexer-anrop och historiken för de senaste indexer-anrop.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Körningshistorik innehåller upp till 50 senaste slutförda körningar, vilket är sorterade i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).

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

## <a name="NextSteps"></a>Nästa steg
Grattis! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Search med en indexerare.

* Läs mer om Azure Cosmos DB i den [service-sidan för Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Läs mer om Azure Search i den [söktjänstsidan](https://azure.microsoft.com/services/search/).
