---
title: Indexering av en Azure Cosmos DB-datakälla för Azure Search | Microsoft Docs
description: Den här artikeln visar hur du skapar en indexerare för Azure Search med en Azure Cosmos DB-datakälla.
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 03/23/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: 165402f5147224cd355f0ae14642069a3de58f19
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Ansluta Cosmos-databas med Azure Search med indexerare

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Konfigurera [Azure Search indexeraren](search-indexer-overview.md) som använder en Azure DB som Cosmos-samling som en datakälla.
> * Skapa en sökindex med JSON-kompatibla datatyper.
> * Konfigurera en indexerare för återkommande indexering och på begäran.
> * Uppdatera inkrementellt index baserat på ändringar i underliggande data.

> [!NOTE]
> Azure Cosmos-DB är nästa generation av DocumentDB. Även om produktnamnet ändras, den `documentdb` syntax i Azure Search indexerare fortfarande finns för bakåtkompatibilitet kompatibilitet i API: er för Azure Search- och portalens sidor. När du konfigurerar indexerare, måste du ange den `documentdb` syntax som finns beskrivet i den här artikeln.

I följande video visar Azure Cosmos DB Programhanteraren Andrew Liu hur du lägger till ett Azure Search-index till en Azure DB som Cosmos-behållare.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>API-typer som stöds

Azure Cosmos DB stöder en mängd olika datamodeller och API: er, utökar Azure Search indexeraren produktionsstöd för SQL-Programmeringsgränssnittet. Stöd för MongoDB API är för närvarande i förhandsversion.  

Stöd för ytterligare API: er är kommande. För att hjälpa oss att prioritera vilka som ska stödja först omvandla din röst på User Voice-webbplatsen:

* [Stöd för tabellen API datakälla](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Stöd för Graph API datakälla](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Stöd för Apache Cassandra API datakälla](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Förutsättningar

Förutom en Cosmos-DB-konto behöver du ha en [Azure Search-tjänsten](search-create-service-portal.md). 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Azure Search indexeraren begrepp

En **datakällan** anger data till index, autentiseringsuppgifter och principer för att identifiera ändringar i data (till exempel ändrade eller borttagna dokument i din samling). Datakällan har definierats som en oberoende resurs så att den kan användas av flera indexerare.

En **indexeraren** beskriver hur data flödar från din datakälla till en mål-sökindexet. En indexerare kan användas för att:

* Utför en enstaka kopia av data att fylla i ett index.
* Synkronisera ett index med ändringar i datakällan enligt ett schema.
* Anropa uppdateringar på begäran till ett index efter behov.

Om du vill konfigurera en indexerare Azure Cosmos DB, måste du skapa ett index datasource och slutligen indexeraren. Du kan skapa dessa objekt med hjälp av den [portal](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search), eller [REST API](/rest/api/searchservice/). 

Den här artikeln visar hur du använder REST API. Om du väljer för portalen, den [guiden Importera data](search-import-data-portal.md) hjälper dig att skapa alla dessa resurser, inklusive indexet.

> [!TIP]
> Du kan starta guiden **Importera data** från Azure Cosmos DB-instrumentpanelen för att förenkla indexeringen för datakällan. Välj **Samlingar** > **Lägg till Azure Search** i navigeringsfältet till vänster för att komma igång.

> [!NOTE] 
> Nu är det går inte att skapa eller redigera **MongoDB** datakällor med hjälp av Azure-portalen eller .NET SDK. Men du **kan** övervaka körningstiden för MongoDB indexerare i portalen.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla
Om du vill skapa en datakälla, gör ett INLÄGG:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
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

Brödtexten i begäran innehåller definitionen av datakällan, som ska innehålla följande fält:

* **namnet**: Välj ett valfritt namn för din databas.
* **typen**: måste vara `documentdb`.
* **autentiseringsuppgifter**:
  
  * **connectionString**: krävs. Ange anslutningsinformation till din Azure Cosmos-DB-databas i följande format: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` MongoDB för samlingar, lägga till **ApiKind = MongoDB** i anslutningssträngen: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDB` 
* **behållaren**:
  
  * **namnet**: krävs. Ange id för samlingen databasen indexeras.
  * **frågan**: valfria. Du kan ange en fråga för att platta ut ett godtyckliga JSON-dokument till en platt schemat som Azure Search kan indexera. Frågor stöds inte för MongoDB-samlingar. 
* **dataChangeDetectionPolicy**: rekommenderas. Se [indexering ändras dokument](#DataChangeDetectionPolicy) avsnitt.
* **dataDeletionDetectionPolicy**: valfria. Se [indexering bort dokument](#DataDeletionDetectionPolicy) avsnitt.

### <a name="using-queries-to-shape-indexed-data"></a>Med hjälp av frågor för att formen indexerade data
Du kan ange en SQL-fråga för att platta ut kapslade egenskaper eller matriser, JSON projektegenskaperna och filtrera data indexeras. 

> [!WARNING]
> Anpassade frågor stöds inte för **MongoDB** samlingar: `container.query` parametern måste anges till null eller utelämnas. Om du behöver använda en anpassad fråga berätta på [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

Filtreringsfrågan:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Förenkla frågan:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Projektion av frågan:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Matrisen förenkling fråga:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Steg 2: Skapa ett index
Skapa ett mål Azure Search index om du inte redan har en. Du kan skapa ett index med hjälp av den [Azure-portalen UI](search-create-index-portal.md), [skapa Index REST API](/rest/api/searchservice/create-index) eller [indexera klassen](/dotnet/api/microsoft.azure.search.models.index).

I följande exempel skapas ett index med ett id och beskrivning fält:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
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

Kontrollera att schemat för mål-index är kompatibel med schemat för JSON-källdokument- eller utdata för anpassad fråga-projektion.

> [!NOTE]
> För partitionerade samlingar är standardnyckeln för dokumentet DB Azure-Cosmos `_rid` -egenskap som Azure Search automatiskt byter till `rid` eftersom fältnamn inte får börja med ett undescore tecken. Dessutom Azure Cosmos DB `_rid` värden innehåller tecken som är ogiltiga i Azure Search-nycklar. Därför kan den `_rid` värden är Base64-kodad.
> 
> För samlingar av MongoDB, Azure Search automatiskt byter namn på den `_id` egenskapen `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappning mellan JSON-datatyper och Azure Search-datatyper
| JSON-datatyp | Kompatibel index fältet måltyper |
| --- | --- |
| Booleskt |Edm.Boolean, Edm.String |
| Siffror som ser ut som heltal |Edm.Int32, Edm.Int64, Edm.String |
| Siffror som ser ut som flytande punkter |Edm.Double, Edm.String |
| Sträng |Edm.String |
| Matriser av primitiva typer, till exempel [”a”, ”b”, ”c”] |Collection(Edm.String) |
| Strängar som ser ut som datum |Edm.DateTimeOffset, Edm.String |
| GeoJSON objekt, till exempel {”typ”: ”Point”, ”coordinates”: [long, lat]} |Edm.GeographyPoint |
| Andra JSON-objekt |Gäller inte |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Steg 3: Skapa en indexerare

När index och datakälla har skapats är du redo att skapa indexeraren:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Indexeraren körs varannan timme (schemaintervallet är inställt på ”PT2H”). Ställa in intervall för ”PT30M” om du vill köra en indexerare var 30: e minut. Den kortaste stöds är 5 minuter. Schemat är valfritt - om detta utelämnas, en indexerare körs bara en gång när den skapas. Du kan dock köra en indexerare på begäran när som helst.   

Mer information om API: et för skapa indexeraren kolla [skapa indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Kör indexeraren på begäran
Förutom att köra med jämna mellanrum enligt ett schema, kan en indexerare anropas på begäran:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> När du kör API returnerar har indexeraren-anrop har schemalagts, men den faktiska bearbetningen sker asynkront. 

Du kan övervaka statusen indexerare i portalen eller med hjälp av hämta indexeraren Status API, vilket beskrivs härnäst. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Hämtar status för indexerare
Du kan hämta status och körningen historiken för en indexerare:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

Svaret innehåller övergripande indexeraren status, senaste (eller pågående) indexeraren anrop och historiken för senaste indexeraren anrop.

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

Körningstiden innehåller upp till 50 senaste slutförda körningar, vilket är sorterade i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexering ändrade dokument
Syftet med en princip för ändra data är att effektivt identifiera ändrade dataobjekt. Den enda stödda principen är för närvarande den `High Water Mark` princip genom att använda den `_ts` () tidsstämpelsegenskapen som tillhandahålls av Azure Cosmos DB som anges enligt följande:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Med den här principen rekommenderas så bra indexeraren prestanda. 

Om du använder en anpassad fråga, kontrollerar du att den `_ts` egenskapen projiceras av frågan.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Inkrementell status och anpassade frågor
Inkrementell status under indexeringen säkerställer att om indexeraren körningen har avbrutits av tillfälliga fel eller tidsgränsen för körning kan välja indexeraren där den avbröts nästa gång den körs, i stället för att indexera hela samlingen från grunden. Detta är särskilt viktigt när indexering stora samlingar. 

Om du vill aktivera inkrementell pågår när du använder en anpassad fråga, se till att din fråga sorterar resultaten av den `_ts` kolumn. Detta gör att regelbundet kontrollera pekar som Azure Search används för att tillhandahålla stegvis pågår med fel.   

I vissa fall, även om frågan innehåller en `ORDER BY [collection alias]._ts` -sats Azure Search kan inte härleda att frågan är sorterade efter den `_ts`. Du kan se Azure Search att resultaten ordnas med hjälp av den `assumeOrderByHighWaterMarkColumn` konfigurationsegenskapen. Om du vill ange den här tipset skapa eller uppdatera indexeraren på följande sätt: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexering bort dokument
När rader tas bort från samlingen vill du normalt ta bort dessa rader från sökindexet samt. Syftet med en identifiering av princip för borttagning av data är att identifiera effektivt borttagna dataobjekt. Den enda stödda principen är för närvarande den `Soft Delete` principen (borttagning är markerade med en flagga av något slag), som anges på följande sätt:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Om du använder en anpassad fråga, se till att egenskapen refererar till `softDeleteColumnName` projiceras av frågan.

I följande exempel skapas en datakälla med en princip för mjuk borttagning:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
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
Grattis! Du har lärt dig hur du integrerar Azure Cosmos DB med Azure Search med hjälp av en indexerare.

* Läs mer om Azure Cosmos DB i den [Azure DB som Cosmos-webbtjänstsida](https://azure.microsoft.com/services/cosmos-db/).
* Läs mer om Azure Search i den [service söksidan](https://azure.microsoft.com/services/search/).
