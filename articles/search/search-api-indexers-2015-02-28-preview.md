---
title: "Indexeringsåtgärder (Azure Söktjänsts-REST API: 2015-02-28-Preview) | Microsoft Docs"
description: "Indexeringsåtgärder (Azure Söktjänsts-REST API: 2015-02-28-Preview)"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: eugenesh
ms.openlocfilehash: 356ceb98106d080d8c24dedc3547bee33750156e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Indexeringsåtgärder (Azure Söktjänsts-REST API: 2015-02-28-Preview)
> [!NOTE]
> Den här artikeln beskriver indexerare i den [2015-02-28-Preview REST API](search-api-2015-02-28-preview.md). Den här API-versionen lägger till förhandsversioner av indexerare i Azure Blob Storage med dokumentet extrahering och Azure Table Storage indexeraren plus andra förbättringar. API: et stöder också allmänt tillgänglig (GA) indexerare, inklusive indexerare för Azure SQL Database, SQL Server på virtuella Azure-datorer och Azure Cosmos DB.
> 
> 

## <a name="overview"></a>Översikt
Azure Search kan integreras direkt med vissa gemensamma datakällor eliminerar behovet av att skriva kod för att indexera dina data. Om du vill konfigurera den här upp, kan du anropa API: et för Azure-sökning för att skapa och hantera **indexerare** och **datakällor**. 

En **indexeraren** är en resurs som ansluter datakällor med mål search-index. En indexerare används på följande sätt: 

* Utför en enstaka kopia av data att fylla i ett index.
* Synkronisera ett index med ändringar i datakällan enligt ett schema. Schemat är en del av definitionen indexeraren.
* Anropa på begäran för att uppdatera ett index efter behov. 

En **indexeraren** är användbart när du vill regelbundna uppdateringar till ett index. Du kan antingen skapa en infogad schema som en del av en indexerare definition eller kör det på begäran med hjälp av [köra indexeraren](#RunIndexer). 

En **datakällan** anger vilka data måste indexeras, autentiseringsuppgifter för att komma åt data och principer för att aktivera Azure Search att effektivt identifiera ändringar i data (exempelvis ändrade eller borttagna rader i en databastabell). Den har definierats som en oberoende resurs så att den kan användas av flera indexerare.

Följande datakällor stöds:

* **Azure SQL Database** och **SQLServer på virtuella Azure-datorer**. Riktad genomgång, se [i den här artikeln](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 
* **Azure Cosmos DB**. Riktad genomgång, se [i den här artikeln](search-howto-index-documentdb.md). 
* **Azure Blob Storage**, inklusive följande dokumentera format: PDF, Microsoft Office (DOCX-dokument, XSLX/XLS, PPTX/PPT, Ignorerad), HTML-, XML, ZIP och oformaterad text filer (inklusive JSON). Riktad genomgång, se [i den här artikeln](search-howto-indexing-azure-blob-storage.md).
* **Azure Table Storage**. Riktad genomgång, se [i den här artikeln](search-howto-indexing-azure-tables.md).

Vi funderar på att lägga till stöd för ytterligare datakällor i framtiden. För att hjälpa oss att prioritera besluten, ange din feedback på den [Azure Search Feedbackforum](http://feedback.azure.com/forums/263029-azure-search).

Se [Tjänstbegränsningarna](search-limits-quotas-capacity.md) för gränser som är relaterade till indexerare och data käll-resurser.

## <a name="typical-usage-flow"></a>Flödet för normal användning
Du kan skapa och hantera indexerare och datakällor via enkel HTTP-begäranden (POST, GET, PUT, DELETE) mot en given `data source` eller `indexer` resurs.

Konfigurera automatisk indexering är vanligtvis en process i fyra steg:

1. Identifiera datakälla som innehåller de data som måste indexeras. Tänk på att Azure Search inte stöder alla datatyperna som finns i datakällan. Se [datatyper som stöds](https://msdn.microsoft.com/library/azure/dn798938.aspx) för listan.
2. Skapa ett Azure Search index vars schema är kompatibel med din datakälla.
3. Skapa en datakälla för Azure Search enligt beskrivningen i [Skapa datakälla](#CreateDataSource).
4. Skapa en indexerare för Azure Search enligt [skapa indexeraren](#CreateIndexer).

Du bör planera för hur du skapar en indexerare för varje mål index och datakälla kombination. Du kan ha flera indexerare skrivning till samma index, och du kan återanvända samma datakälla för flera indexerare. Men en indexerare kan bara använda en datakälla i taget och kan bara skriva till ett index. 

När du har skapat en indexerare, du kan hämta dess körning status genom att använda den [Erhåll Status för indexeraren](#GetIndexerStatus) igen. Du kan också köra en indexerare på alla tid (i stället för eller förutom körs med jämna mellanrum enligt ett schema) med hjälp av den [köra indexeraren](#RunIndexer) igen.

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Skapa datakälla
I Azure Search används en datakälla med indexerare som tillhandahåller anslutningsinformationen för ad hoc- eller schemalagd datauppdatering för ett mål-index. Du kan skapa en ny datakälla i en Azure Search-tjänst med en HTTP POST-begäran.

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan också använda PUT och ange namnet på datakällan på URI: N. Om datakällan inte finns, skapas.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> Det maximala antalet datakällor som tillåts varierar beroende på prisnivå. Tjänsten gratis kan upp till 3-datakällor. Standard-tjänsten tillåter 50 datakällor. Se [Tjänstbegränsningarna](search-limits-quotas-capacity.md) mer information.
> 
> 

**Förfrågan**

HTTPS krävs för alla tjänstbegäranden. Den **Skapa datakälla** begäran kan konstrueras med en POST eller PUT metod. När du använder POST, måste du ange en datakälla i begärandetexten tillsammans med definitionen av datakällan. Med PUT är namnet en del av URL: en. Om datakällan inte finns, skapas. Om det redan finns uppdateras den till den nya definitionen. 

Datakällnamnet måste vara gemener, börja med en bokstav eller siffra, har inga snedstreck eller punkter och vara mindre än 128 tecken. När du har startat datakällans namn med en bokstav eller siffra kan resten av namnet innehålla en bokstav, antal och tankstreck, så länge streck inte är i följd. Se [namngivningsregler](https://msdn.microsoft.com/library/azure/dn857353.aspx) mer information.

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`.

**Huvuden för begäran**

I följande lista beskrivs de obligatoriska och valfria begärandehuvudena. 

* `Content-Type`: Krävs. Ställ in på`application/json`
* `api-key`: Krävs. Den `api-key` används för att autentisera begäran om att din söktjänst. Det är ett strängvärde som unik för din tjänst. Den **Skapa datakälla** begäran måste innehålla en `api-key` huvudet inställt till din administrationsnyckel (i stället för en fråga nyckel). 

Du måste också tjänstnamnet att konstruera den begärda Webbadressen. Du kan hämta både tjänstnamnet och `api-key` från instrumentpanelen tjänsten i den [Azure Portal](https://portal.azure.com/). Se [skapa en söktjänst i portalen](search-create-service-portal.md) sidan navigering hjälp.

<a name="CreateDataSourceRequestSyntax"></a>
**Begäran brödtext Syntax**

Brödtexten i begäran innehåller en definition av datakällan, som innehåller typen av datakälla, autentiseringsuppgifter för att läsa data, samt ett valfritt data ändra identifiering och borttagning identifiering principer data som används för att identifiera effektivt ändras eller tas bort data i datakällan när det används med en regelbundet schemalagda indexeraren. 

Syntaxen för att strukturera nyttolasten i begäran är som följer. Ett exempel på en begäran har angetts mer på i det här avsnittet.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Begäran innehåller följande egenskaper: 

* `name`: Krävs. Namnet på datakällan. En datakälla måste bara innehålla gemena bokstäver, siffror eller bindestreck, kan inte börja eller sluta med bindestreck och får innehålla högst 128 tecken.
* `description`: En valfri beskrivning. 
* `type`: Krävs. Måste vara något av stöds datakälltyp:
  * `azuresql`-Azure SQL Database eller SQLServer på virtuella Azure-datorer
  * `documentdb`-Cosmos azure DB
  * `azureblob`-Azure Blob Storage
  * `azuretable`-Azure Table Storage
* `credentials`:
  * Det obligatoriska `connectionString` egenskapen anger anslutningssträngen för datakällan. Formatet för anslutningssträngen beror på typen av datakälla: 
    * Detta är den vanliga SQL Server-anslutningssträngen för Azure SQL. Om du använder Azure-portalen för att hämta anslutningssträngen ska du använda den `ADO.NET connection string` alternativet.
    * För Azure Cosmos DB anslutningssträngen måste vara i följande format: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Alla värden är obligatoriska. Du hittar dem i den [Azure-portalen](https://portal.azure.com/).  
    * För Azure Blob och Table Storage är lagringsanslutningssträngen för kontot. Formatet som beskrivs [här](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). HTTPS-slutpunkt-protokollet krävs.  
* `container`, krävs: Anger vilka data att indexera med hjälp av den `name` och `query` egenskaper: 
  * `name`, krävs:
    * Azure SQL: Anger den tabell eller vy. Du kan använda schemat kvalificerade namn som `[dbo].[mytable]`.
    * DocumentDB: Anger samlingen. 
    * Azure Blob Storage: Anger vilken lagringsbehållare.
    * Azure Table Storage: Anger namnet på tabellen. 
  * `query`, valfritt:
    * DocumentDB: kan du ange en fråga som förenklar en godtyckliga JSON-dokumentlayout till en platt schemat som Azure Search kan indexera.  
    * Azure Blob Storage: kan du ange en virtuell mapp i blob-behållaren. Till exempel för blobbsökvägen `mycontainer/documents/blob.pdf`, `documents` kan användas som virtuella mappen.
    * Azure Table Storage: kan du ange en fråga som filtrerar uppsättningen rader som ska importeras.
    * Azure SQL: frågan stöds inte. Om du behöver den här funktionen, rösta på [förslaget](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
* Det valfria `dataChangeDetectionPolicy` och `dataDeletionDetectionPolicy` egenskaperna beskrivs nedan.

<a name="DataChangeDetectionPolicies"></a>
**Principer för data ändras identifiering**

Syftet med en princip för ändra data är att effektivt identifiera ändrade dataobjekt. Stöds principer varierar beroende på typ av datakälla. I nedanstående avsnitt beskrivs varje princip. 

***Ändra identifiering för Högvattenmärkesprincip*** 

Använd den här principen när datakällan innehåller en kolumn eller en egenskap som uppfyller följande kriterier:

* Alla infogningar ange ett värde för kolumnen. 
* Alla uppdateringar till ett objekt kan du också ändra värdet för kolumnen. 
* Värdet för den här kolumnen ökar med varje ändring.
* Frågor som använder en filtersats som liknar följande `WHERE [High Water Mark Column] > [Current High Water Mark Value]` kan köras effektivt.

Till exempel när du använder Azure SQL-datakällor som en indexerad `rowversion` kolumnen är den perfekta kandidaten för användning med med vattenmärke för principen. 

Den här principen kan anges på följande sätt:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

När du använder Azure Cosmos DB-datakällor, måste du använda den `_ts` egenskapen som tillhandahålls av Azure Cosmos DB. 

När du använder Azure Blob-datakällor, Azure Search automatiskt använder en övre gräns ändra princip baserat på en blob last-modified tidsstämpel; Du behöver inte ange en sådan policy själv.   

***SQL-integrerad ändra princip***

Om SQL-databasen stöder [ändringsspårning](https://msdn.microsoft.com/library/bb933875.aspx), bör du använda SQL integrerad ändra Spårningsprincip. Den här principen gör det mest effektiva ändringsspårning och att Azure-sökning för att identifiera borttagna rader utan att ha en explicit ”mjuk borttagning”-kolumn i schemat.

Integrerad ändringsspårning stöds från och med följande versioner för SQL Server-databasen: 

* SQL Server 2008 R2, om du använder SQL Server på Azure Virtual Machines.
* Azure SQL Database V12, om du använder Azure SQL Database.  

Ta bort rader med hjälp av SQL integrerad ändringsspårning princip inte anger en separat data princip för identifiering av borttagning - principen har inbyggt stöd för att identifiera när. 

Den här principen kan bara användas med tabeller. den kan inte användas med vyer. Du måste aktivera ändringsspårning för tabellen som du använder innan du kan använda den här principen. Se [aktivera och inaktivera ändringsspårning](https://msdn.microsoft.com/library/bb964713.aspx) anvisningar.    

När du strukturerar den **Skapa datakälla** begära SQL integrerad ändringsspårning princip kan anges på följande sätt:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Data med principer för borttagning**

Syftet med en identifiering av princip för borttagning av data är att identifiera effektivt borttagna dataobjekt. Den enda stödda principen är för närvarande den `Soft Delete` policy som låter identifierar borttagna objekt baserat på värdet för en `soft delete` kolumn eller en egenskap i datakällan. Den här principen kan anges på följande sätt:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> Endast kolumner med sträng, heltal eller booleska värden stöds. Det värde som används som `softDeleteMarkerValue` måste vara en sträng, även om motsvarande kolumn innehåller heltal eller booleska värden. Till exempel om värdet som visas i datakällan är 1 använder `"1"` som den `softDeleteMarkerValue`.    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**Begäran Body-exempel**

Om du tänker använda datakällan med en indexerare som körs enligt ett schema i det här exemplet illustrerar hur du anger principer för ändrings- och borttagning: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Om du endast vill använda datakällan för en kopia av data kan principerna utelämnas:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Svar**

För en lyckad begäran: 201 skapad. 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>Uppdatera datakällan
Du kan uppdatera en befintlig datakälla med hjälp av en HTTP PUT-begäran. Du kan ange namnet på datakällan för att uppdatera på URI-begäran:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. [Azure Search-API-versioner](https://msdn.microsoft.com/library/azure/dn864560.aspx) har mer information om alternativa versioner.

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Förfrågan**

Begäran brödtext syntax är desamma som för [Skapa datakälla begär](#CreateDataSourceRequestSyntax).

Vissa egenskaper kan inte uppdateras på en befintlig datakälla. Exempelvis kan du ändra typ av en befintlig datakälla.  

Om du inte vill ändra anslutningssträngen för en befintlig datakälla, kan du ange literalen `<unchanged>` för anslutningssträngen. Det här är användbart i situationer där du behöver uppdatera data source men har inte åtkomst till anslutningssträngen eftersom det är känsliga data.

**Svar**

För en lyckad begäran: 201 Skapad om en ny datakälla har skapats och 204 Nej innehåll om en befintlig datakälla har uppdaterats.

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>Lista över datakällor
Den **lista datakällor** åtgärd returnerar en lista över datakällor i Azure Search-tjänsten. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

För en lyckad begäran: 200 OK.

Här är ett exempel svarstexten:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Observera att du kan filtrera svaret till bara de egenskaper som du är intresserad av. Till exempel om du vill att endast en lista över namn på datakällor måste använda OData `$select` frågealternativet:

    GET /datasources?api-version=205-02-28&$select=name

I det här fallet visas svaret från exemplet ovan på följande sätt: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Detta är en teknik som är användbart för att spara bandbredd om du har en stor mängd datakällor i din söktjänst.

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>Hämta datakälla
Den **hämta datakällan** åtgärden hämtar definitionen av datakällan från Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Svaret är liknande exemplen i [Skapa datakälla exempel begäranden](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> Ange inte den `Accept` begärandehuvudet till `application/json;odata.metadata=none` när anropa denna API som gör det kommer `@odata.type` attribut som ska uteslutas från svaret och du kommer inte att kunna skilja mellan ändring av data och principer identifiering för borttagning av olika typer. 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>Ta bort datakällan
Den **ta bort datakällan** åtgärden tar bort en datakälla från Azure Search-tjänsten.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> Om en indexerare refererar till datakällan som du tar bort, fortsätter fortfarande borttagningen. Men övergår dessa indexerare i feltillstånd när deras nästa körning.  
> 
> 

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 204 Nej innehåll returneras för ett lyckat svar.

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>Skapa indexerare
Du kan skapa en ny indexerare i en Azure Search-tjänst med en HTTP POST-begäran.

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan också använda PUT och ange namnet på datakällan på URI: N. Om datakällan inte finns, skapas.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> Det maximala antalet tillåtna indexerare varierar beroende på prisnivå. Tjänsten gratis kan upp till 3 indexerare. Standard-tjänsten tillåter 50 indexerare. Se [Tjänstbegränsningarna](search-limits-quotas-capacity.md) mer information.
> 
> 

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

<a name="CreateIndexerRequestSyntax"></a>
**Begäran brödtext Syntax**

Brödtexten i begäran innehåller en definition av indexerare som anger datakällan och mål-index för indexering, samt valfritt indexeringsschema och parametrar. 

Syntaxen för att strukturera nyttolasten i begäran är som följer. Ett exempel på en begäran har angetts mer på i det här avsnittet.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Indexerschemat**

En indexerare kan du ange ett schema. Om det finns ett schema körs indexeraren med jämna mellanrum enligt schemat. Schemat har följande attribut:

* `interval`: Krävs. Duration-värde som anger ett intervall eller tid för indexeraren körs. Minsta tillåtna intervall är 5 minuter. den längsta är en dag. Den måste formateras som ett XSD ”daytimeduration” XSD-värde (en begränsad delmängd av ett [ISO 8601 varaktighet](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värdet). Mönstret för detta är: `"P[nD][T[nH][nM]]"`. Exempel: `PT15M` för varje kvart `PT2H` för varannan timme. 
* `startTime`: Krävs. En UTC-datum och tid när indexeraren ska börja köra. 

**Indexerare parametrar**

En indexerare kan du ange flera parametrar som påverkar dess beteende. Alla parametrar är valfria.  

* `maxFailedItems`: Antalet objekt som kan misslyckas med att indexera innan du kör en indexerare anses vara fel. Standardvärdet är 0. Information om misslyckade artiklarna returneras av den [Erhåll Status för indexeraren](#GetIndexerStatus) igen. 
* `maxFailedItemsPerBatch`: Antalet objekt som kan misslyckas med att indexera i varje batch innan du kör en indexerare anses vara fel. Standardvärdet är 0.
* `base64EncodeKeys`: Anger huruvida dokumentet nycklarna är Base64-kodad. Azure Search begränsar dock det antal tecken som kan ingå i en Dokumentnyckel. Värden i källdata kan dock innehålla tecken som är ogiltiga. Om det är nödvändigt att indexera sådana värden som dokumentet nycklar, den här flaggan anges till true. Standardvärdet är `false`.
* `batchSize`: Anger antalet objekt som läses från datakällan och indexeras som en enskild batch för att förbättra prestanda. Standardvärdet beror på typen av datakälla: det är 1000 för Azure SQL och Azure Cosmos DB och 10 för Azure Blob Storage.

**Fältmappningar**

Du kan använda fältmappningar för att mappa namnet på ett fält i datakällan till ett annat fältnamn i mål-index. Anta till exempel att en källtabell med ett fält `_id`. Azure Search kan inte namnet på ett fält som börjar med ett understreck, så du måste byta namn på fältet. Detta kan göras med hjälp av den `fieldMappings` egenskapen indexeraren på följande sätt: 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Du kan ange flera fältmappningar: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Både käll- och fältnamn är inte skiftlägeskänsliga.

<a name="FieldMappingFunctions"></a>
***Fältet mappning funktioner***

Fältmappningar kan också användas för att omvandla källa fältvärden med *mappning funktioner*.

Endast en sådan funktion stöds: `jsonArrayToStringCollection`. Den Parsar ett fält som innehåller en sträng formaterad som en JSON-matris till ett Collection(Edm.String) fält i mål-index. Den är avsedd för användning med Azure SQL indexeraren särskilt eftersom SQL inte har en inbyggd samlingsdatatyp. Den kan användas på följande sätt: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Om fältet i datakällan som innehåller strängen till exempel `["red", "white", "blue"]`, sedan målfältet av typen `Collection(Edm.String)` fylls med tre värden `"red"`, `"white"` och `"blue"`.

Observera att den `targetFieldName` egenskapen är valfri, om den lämnas ut, den `sourceFieldName` värdet används. 

<a name="CreateIndexerRequestExamples"></a>
**Begäran Body-exempel**

I följande exempel skapas en indexerare som kopierar data från tabellen som refereras av den `ordersds` datakällan till den `orders` index enligt ett schema som börjar på 1 januari 2015 UTC och körs varje timme. Varje anrop av indexerare kommer att lyckas om mer än 5 objekt misslyckas med att indexera i varje batch och högst 10 objekt misslyckas med att indexera totalt. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Svar**

201 skapad för en lyckad begäran.

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>Uppdatera indexeraren
Du kan uppdatera en befintlig indexerare med hjälp av en HTTP PUT-begäran. Du kan ange namnet på indexeraren att uppdatera på URI-begäran:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Den `api-version` krävs. Den aktuella versionen är `2015-02-28`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Förfrågan**

Begäran brödtext syntax är desamma som för [skapa indexeraren begär](#CreateIndexerRequestSyntax).

**Svar**

För en lyckad begäran: 201 Skapad om en ny indexeraren har skapats och 204 Nej innehåll om en befintlig indexerare har uppdaterats.

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>Lista indexerare
Den **lista indexerare** åtgärd returnerar listan över indexerare i Azure Search-tjänsten. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. [Azure Search versionshantering](https://msdn.microsoft.com/library/azure/dn864560.aspx) har mer information om alternativa versioner.

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

För en lyckad begäran: 200 OK.

Här är ett exempel svarstexten:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Observera att du kan filtrera svaret till bara de egenskaper som du är intresserad av. Till exempel om du vill att endast en lista över indexeraren namn använda OData `$select` frågealternativet:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

I det här fallet visas svaret från exemplet ovan på följande sätt: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Detta är en användbar teknik för att spara bandbredd om du har många av indexerare i din söktjänst.

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>Hämta indexeraren
Den **hämta indexeraren** åtgärden hämtar indexeraren definitionen från Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 200 OK returneras för ett lyckat svar.

Svaret är liknande exemplen i [skapa indexeraren exempel begäranden](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>Ta bort indexeraren
Den **ta bort indexeraren** åtgärden tar bort en indexerare från din Azure Search-tjänst.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

När du tar bort en indexerare indexeraren körningar pågår som för närvarande körs klart, men inga ytterligare körningar kommer att schemaläggas. Försök att använda en obefintlig indexeraren leder till att HTTP-statuskod 404 hittades. 

Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 204 Nej innehåll returneras för ett lyckat svar.

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>Köra indexeraren
Förutom körs med jämna mellanrum enligt ett schema, kan en indexerare också startas på begäran via den **köra indexeraren** igen: 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 202 godkända returneras för ett lyckat svar.

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Hämta Status för indexerare
Den **Erhåll Status för indexeraren** åtgärden hämtar aktuell status och körningen historiken för en indexerare: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 200 OK för ett lyckat svar.

Svarstexten innehåller information om hälsostatus övergripande indexerare, senaste indexeraren aktivering samt historiken för senaste indexeraren anrop (om sådan finns). 

Ett exempel svarstexten ser ut så här: 

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

**Status för indexerare**

Indexerare status kan vara något av följande värden:

* `running`Anger att indexeraren körs normalt. Observera att vissa av indexerare körningar kan fortfarande misslyckas, så det är en bra idé att kontrollera den `lastResult` egenskapen samt. 
* `error`Anger att indexeraren uppstod ett fel som inte kan korrigeras utan mänsklig inblandning. Till exempel av autentiseringsuppgifter för datakälla kan ha gått ut eller schemat för datakällan eller målindexet har ändrats i en ny sätt. 

**Indexerare resultat av utförande**

Ett resultat av utförande indexeraren innehåller information om en enda indexeraren körning. Senaste resultat har angetts som den `lastResult` egenskapen indexeraren status. Andra senaste, om den finns, returneras resultatet som den `executionHistory` egenskapen indexeraren status. 

Resultat av utförande indexeraren innehåller följande egenskaper: 

* `status`: status för en körning. Se [Körstatus för indexeraren](#IndexerExecutionStatus) nedan för information. 
* `errorMessage`: felmeddelande för en misslyckad körning. 
* `startTime`: tid i UTC när denna startades.
* `endTime`: tid i UTC när den här körning avslutad. Det här värdet anges inte om körningen pågår fortfarande.
* `errors`: en matris med objektnivå fel, om sådana finns. Varje post innehåller en Dokumentnyckel (`key` egenskapen) och felmeddelandet (`errorMessage` egenskap). 
* `itemsProcessed`: antalet datakällan objekt (till exempel tabellraderna) som indexeraren gjordes ett försök att indexera den här köras. 
* `itemsFailed`: antalet objekt som misslyckades under den här körningen.  
* `initialTrackingState`: alltid `null` för indexeraren första körningen, eller om data ändras spårning princip är inte aktiverat på den datakälla som används. Om en sådan policy är aktiverad i efterföljande körningar det här värdet anger den första (lägsta) ändringsspårning värdet som bearbetas av denna. 
* `finalTrackingState`: alltid `null` om data ändras spårning princip är inte aktiverat på den datakälla som används. Annars visar senaste (högsta) ändringsspårning värdet som har bearbetats av denna. 

<a name="IndexerExecutionStatus"></a>
**Status för indexerare**

Indexerare Körstatus samlar in statusen för en enskild indexeraren körning. Det kan ha följande värden:

* `success`Anger att indexeraren körningen har slutförts.
* `inProgress`Anger att indexeraren-körning pågår. 
* `transientFailure`Anger att en indexerare körning misslyckades. Se `errorMessage` -egenskapen för information. Felet kanske eller kanske inte kräver mänsklig inblandning åtgärda - exempelvis åtgärda ett schemainkompatibilitet mellan datakällan och mål-index kräver användaråtgärd, men inte av ett tillfälligt datakälla driftstopp. Anrop av indexerare fortsätter per schema, om en sådan finns. 
* `persistentFailure`Anger att indexeraren misslyckades på ett sätt som kräver mänsklig inblandning. Schemalagda indexeraren körningar stoppa. Efter det att problemet använder du återställa indexeraren API för att starta om de schemalagda körningar. 
* `reset`Anger att indexeraren har återställts av ett anrop för att återställa indexeraren API (se nedan). 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>Återställa indexeraren
Den **återställa indexeraren** åtgärden återställer ändringsspårning tillstånd är kopplat till indexeraren. På så sätt kan du starta från början omindexering (till exempel om din datakällans schema har ändrats) eller ändra data identifiering ändringsprinciper för en datakälla som är kopplade till indexeraren.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

Den `api-version` krävs. Förhandsversionen är `2015-02-28-Preview`. 

Den `api-key` måste vara en administrationsnyckel (i stället för en fråga nyckel). Se autentiseringsavsnittet i [Söktjänsts-REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) för mer information om nycklar. [Skapa en söktjänst i portalen](search-create-service-portal.md) förklarar hur du hämtar service-URL och nyckelegenskaper som används i begäran.

**Svar**

Statuskod: 204 Nej innehåll för ett lyckat svar.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappning mellan SQL-datatyper och Azure Search-datatyper
<table style="font-size:12">
<tr>
<td>SQL-datatypen</td>    
<td>Tillåtna målindexet fälttyp</td>
<td>Anteckningar</td>
</tr>
<tr>
<td>bitar</td>
<td>Edm.Boolean Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32 Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64 Edm.String</td>
<td></td>
</tr>
<tr>
<td>verkliga, float</td>
<td>Edm.Double Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney pengar<br>Decimal<br>numeriskt
</td>
<td>Edm.String</td>
<td>Azure Search har inte stöd för konvertering av decimaltyper till Edm.Double eftersom detta skulle förlora precision
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Se [fältet mappning funktioner](#FieldMappingFunctions) i det här dokumentet för mer information om hur du omvandla en strängkolumn till en Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geografisk plats</td>
<td>Edm.GeographyPoint</td>
<td>Stöds endast geografi instanser av typen plats med SRID 4326 (som är standard)</td>
</tr>
<tr>
<td>ROWVERSION</td>
<td>Saknas</td>
<td>Radversioner kolumner kan inte lagras i sökindexet, men de kan användas för ändringsspårning</td>
</tr>
<tr>
<td>tid, timespan<br>Binary, varbinary, bild,<br>XML-, geometri, CLR-typer</td>
<td>Saknas</td>
<td>Stöds inte</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappning mellan JSON-datatyper och Azure Search-datatyper
<table style="font-size:12">
<tr>
<td>JSON-datatyp</td>    
<td>Tillåtna målindexet fälttyp</td>
<td>Anteckningar</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean Edm.String</td>
<td></td>
</tr>
<tr>
<td>Integrerad siffror</td>
<td>Edm.Int32 Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Flyttal</td>
<td>Edm.Double Edm.String</td>
<td></td>
</tr>
<tr>
<td>Sträng</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>matriser av primitiva typer, t.ex. [”a”, ”b”, ”c”]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Strängar som ser ut som datum</td>
<td>Edm.DateTimeOffset Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON point-objekt</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON punkter är JSON-objekt i följande format: {”typ”: ”Point”, ”coordinates”: [long, lat]} </td>
</tr>
<tr>
<td>Andra JSON-objekt</td>
<td>Saknas</td>
<td>Stöds inte; Azure Search stöder för närvarande endast primitiva typer och strängen samlingar</td>
</tr>
</table>
