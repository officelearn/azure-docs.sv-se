---
title: Sök över Azure SQL-data
titleSuffix: Azure Cognitive Search
description: Importera data från Azure SQL Database med indexerare för fulltextsökning i Azure Cognitive Search. Den här artikeln innehåller anslutningar, indexeringskonfiguration och datainmatning.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113301"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Ansluta till och indexera Azure SQL Database-innehåll med hjälp av en Azure Cognitive Search-indexerare

Innan du kan fråga ett [Azure Cognitive Search-index](search-what-is-an-index.md)måste du fylla i det med dina data. Om data finns i en Azure SQL-databas kan en **Azure Cognitive Search-indexerare för Azure SQL Database** (eller Azure SQL **indexerer** för kort) automatisera indexeringsprocessen, vilket innebär mindre kod att skriva och mindre infrastruktur att ta hand om.

Den här artikeln beskriver mekaniken för att använda [indexerare](search-indexer-overview.md), men beskriver även funktioner som endast är tillgängliga med Azure SQL-databaser (till exempel integrerad ändringsspårning). 

Förutom Azure SQL-databaser tillhandahåller Azure Cognitive Search indexerare för [Azure Cosmos DB,](search-howto-index-cosmosdb.md) [Azure Blob storage](search-howto-indexing-azure-blob-storage.md)och [Azure-tabelllagring](search-howto-indexing-azure-tables.md). Om du vill begära support för andra datakällor kan du ge din feedback på [feedbackforumet](https://feedback.azure.com/forums/263029-azure-search/)för Azure Cognitive Search .

## <a name="indexers-and-data-sources"></a>Indexerare och datakällor

En **datakälla** anger vilka data som ska indexeras, autentiseringsuppgifter för dataåtkomst och principer som effektivt identifierar ändringar i data (nya, ändrade eller borttagna rader). Den definieras som en oberoende resurs så att den kan användas av flera indexerare.

En **indexerare** är en resurs som ansluter en enda datakälla med ett riktat sökindex. En indexerare används på följande sätt:

* Utför en engångskopia av data för att fylla i ett index.
* Uppdatera ett index med ändringar i datakällan enligt ett schema.
* Kör på begäran för att uppdatera ett index efter behov.

En enda indexerare kan bara använda en tabell eller vy, men du kan skapa flera indexerare om du vill fylla i flera sökindex. Mer information om begrepp finns i [Indexeroperationer: Typiskt arbetsflöde](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Du kan konfigurera en Azure SQL-indexerare med hjälp av:

* Guiden Importera data i [Azure-portalen](https://portal.azure.com)
* Azure Cognitive Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* AZURE Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

I den här artikeln använder vi REST API för att skapa **indexerare** och **datakällor**.

## <a name="when-to-use-azure-sql-indexer"></a>När ska Azure SQL Indexer användas
Beroende på flera faktorer som rör dina data kan det hända att användningen av Azure SQL-indexeraren är lämplig. Om dina data uppfyller följande krav kan du använda Azure SQL-indexeraren.

| Kriterie | Information |
|----------|---------|
| Data kommer från en enda tabell eller vy | Om data är utspridda över flera tabeller kan du skapa en enda vy av data. Om du använder en vy kan du dock inte använda sql server-integrerad ändringsidentifiering för att uppdatera ett index med inkrementella ändringar. Mer information finns i [Fånga ändrade och borttagna rader](#CaptureChangedRows) nedan. |
| Datatyperna är kompatibla | De flesta men inte alla SQL-typer stöds i ett Azure Cognitive Search-index. En lista finns i [Mappa datatyper](#TypeMapping). |
| Datasynkronisering i realtid krävs inte | En indexerare kan indexera om tabellen högst var femte minut. Om dina data ändras ofta och ändringarna måste återspeglas i indexet inom några sekunder eller enstaka minuter rekommenderar vi att du använder [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) eller [.NET SDK](search-import-data-dotnet.md) för att skicka uppdaterade rader direkt. |
| Inkrementell indexering är möjlig | Om du har en stor datauppsättning och planerar att köra indexeraren enligt ett schema måste Azure Cognitive Search effektivt kunna identifiera nya, ändrade eller borttagna rader. Icke-inkrementell indexering är endast tillåten om du indexerar på begäran (inte enligt schemat) eller indexerar färre än 100 000 rader. Mer information finns i [Fånga ändrade och borttagna rader](#CaptureChangedRows) nedan. |

> [!NOTE] 
> Azure Cognitive Search stöder endast SQL Server-autentisering. Om du behöver stöd för Azure Active Directory Password-autentisering, rösta på det här [UserVoice-förslaget](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Skapa en Azure SQL-indexerare

1. Skapa datakällan:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Du kan hämta anslutningssträngen från [Azure-portalen](https://portal.azure.com). använda `ADO.NET connection string` alternativet.

2. Skapa målet Azure Cognitive Search index om du inte redan har en. Du kan skapa ett index med hjälp av [portalen](https://portal.azure.com) eller [API:et Skapa index](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Kontrollera att schemat för ditt målindex är kompatibelt med schemat för källtabellen - se [mappning mellan SQL- och Azure Cognitive search datatyper](#TypeMapping).

3. Skapa indexeraren genom att ge den ett namn och referera till datakällan och målindexet:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

En indexerare som skapats på det här sättet har inget schema. Den körs automatiskt en gång när den skapas. Du kan köra den igen när som helst med hjälp av en **körningsindexeringsbegäran:**

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Du kan anpassa flera aspekter av indexeringsbeteende, till exempel batchstorlek och hur många dokument som kan hoppas över innan en indexeringskörning misslyckas. Mer information finns i [Skapa Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Du kan behöva tillåta Azure-tjänster att ansluta till databasen. Se [Ansluta från Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för instruktioner om hur du gör det.

Om du vill övervaka indexerarens status och körningshistorik (antal indexerade objekt, fel osv.) använder du en **indexerarstatusbegäran:**

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Svaret bör se ut ungefär så här:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Körningshistorik innehåller upp till 50 av de senast slutförda avrättningarna, som sorteras i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).
Ytterligare information om svaret finns i [Hämta indexerarstatus](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Kör indexerare enligt ett schema
Du kan också ordna att indexeraren körs med jämna mellanrum enligt ett schema. Det gör du genom att lägga till **schemaegenskapen** när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en PUT-begäran om att uppdatera indexeraren:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Intervallparametern** krävs. Intervallet refererar till tiden mellan början av två på varandra följande indexeringskörningar. Det minsta tillåtna intervallet är 5 minuter; den längsta är en dag. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [VARAKTIGHETSVÄRDE FÖR ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Mönstret för detta `P(nD)(T(nH)(nM))`är: . Exempel: `PT15M` för varje 15 minuter, `PT2H` för varje 2 timmar.

Mer information om hur du definierar indexeringsscheman finns i [Så här schemalägger du indexerare för Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Samla in nya, ändrade och borttagna rader

Azure Cognitive Search använder **inkrementell indexering** för att undvika att behöva indexera om hela tabellen eller visa varje gång en indexerare körs. Azure Cognitive Search innehåller två principer för ändringsidentifiering för att stödja inkrementell indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL-integrerad ändringsspårningsprincip
Om SQL-databasen stöder [ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)rekommenderar vi att du använder **SQL Integrated Change Tracking Policy**. Detta är den mest effektiva politiken. Dessutom kan Azure Cognitive Search identifiera borttagna rader utan att du behöver lägga till en explicit "mjuk borttagning" kolumn i tabellen.

#### <a name="requirements"></a>Krav 

+ Krav på databasversion:
  * SQL Server 2012 SP3 och senare, om du använder SQL Server på virtuella Azure-datorer.
  * Azure SQL Database V12, om du använder Azure SQL Database.
+ Endast tabeller (inga vyer). 
+ Aktivera [ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) för tabellen i databasen. 
+ Ingen sammansatt primärnyckel (en primärnyckel som innehåller mer än en kolumn) i tabellen.  

#### <a name="usage"></a>Användning

Så här använder du den här principen genom att skapa eller uppdatera datakällan så här:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

När du använder SQL-integrerad ändringsspårningsprincip ska du inte ange en separat princip för identifiering av databorttagning – den här principen har inbyggt stöd för att identifiera borttagna rader. För att borttagningarna ska identifieras "automagiskt" måste dock dokumentnyckeln i sökindexet vara samma som primärnyckeln i SQL-tabellen. 

> [!NOTE]  
> När du använder [TRUNKATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) för att ta bort ett stort antal rader från en SQL-tabell måste indexeraren [återställas](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) för att återställa ändringsspårningstillståndet för att hämta radborttagningar.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Identifieringsprincip för ändring av högvattenmärke

Den här ändringsidentifieringsprincipen är beroende av en kolumn med "hög vattenmärke" som fångar den version eller tid då en rad senast uppdaterades. Om du använder en vy måste du använda en princip med högt vattenmärke. Högvattenmärkespelaren måste uppfylla följande krav.

#### <a name="requirements"></a>Krav 

* Alla infogningar anger ett värde för kolumnen.
* Alla uppdateringar av ett objekt ändrar också värdet för kolumnen.
* Värdet i den här kolumnen ökar med varje infogning eller uppdatering.
* Frågor med följande WHERE- och ORDER BY-satser kan utföras effektivt:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Vi rekommenderar starkt att du använder [datatypen rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) för högvattenmärkeskolonnen. Om någon annan datatyp används är ändringsspårning inte garanterat att samla in alla ändringar i närvaro av transaktioner som körs samtidigt med en indexerarfråga. När du använder **rowversion** i en konfiguration med skrivskyddade repliker måste du peka indexeraren på den primära repliken. Endast en primär replik kan användas för datasynkroniseringsscenarier.

#### <a name="usage"></a>Användning

Om du vill använda en princip för högvattenmärke skapar eller uppdaterar du datakällan så här:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Om källtabellen inte har något index på kolumnen högvattenmärke kan frågor som används av SQL-indexeraren ta time out. I synnerhet `ORDER BY [High Water Mark Column]` kräver satsen ett index för att köras effektivt när tabellen innehåller många rader.
>
>

Om du stöter på tidsutsändelsfel kan du använda konfigurationsinställningen `queryTimeout` för indexeraren för att ange tidsgränsen för fråga till ett värde som är högre än standardtidsgränsen på 5 minuter. Om du till exempel vill ange tidsgränsen till 10 minuter skapar eller uppdaterar du indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Du kan också `ORDER BY [High Water Mark Column]` inaktivera satsen. Detta rekommenderas dock inte eftersom om indexeringskörningen avbryts av ett fel, måste indexeraren bearbeta alla rader igen om den körs senare - även om indexeraren redan har bearbetat nästan alla rader när den avbröts. Om du `ORDER BY` vill inaktivera `disableOrderByHighWaterMarkColumn` satsen använder du inställningen i indexeringsdefinitionen:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Principen för identifiering av borttagning av mjuk borttagning
När rader tas bort från källtabellen vill du förmodligen också ta bort raderna från sökindexet. Om du använder sql-principen för integrerad ändringsspårning tas detta hand om åt dig. Principen för spårning av ändringar med högvattenmärke hjälper dig dock inte med borttagna rader. Vad bör jag göra?

Om raderna tas bort fysiskt från tabellen kan Azure Cognitive Search inte dra slutsatsen att det finns poster som inte längre finns.  Du kan dock använda "soft-delete"-tekniken för att logiskt ta bort rader utan att ta bort dem från tabellen. Lägg till en kolumn i tabellen eller visa och markera rader som borttagna med den kolumnen.

När du använder mjukborttagningstekniken kan du ange principen för mjuk borttagning enligt följande när du skapar eller uppdaterar datakällan:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** måste vara en sträng – använd strängrepresentationen av ditt faktiska värde. Om du till exempel har en heltalskolumn där borttagna `"1"`rader markeras med värdet 1 använder du . Om du har en BIT-kolumn där borttagna rader markeras med `True` det `true`booleska sanna värdet använder du stränglitteralen eller , ärendet spelar ingen roll.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mappning mellan SQL- och Azure Cognitive Search-datatyper
| SQL-datatyp | Tillåtna målindexfälttyper | Anteckningar |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| verkliga, flyta |Edm.Double, Edm.String | |
| smallmoney, pengar decimal numeriska |Edm.String |Azure Cognitive Search stöder inte konvertering av decimaltyper till Edm.Double eftersom detta skulle förlora precision |
| röding, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |En SQL-sträng kan användas för att fylla i ett samlingsfält(Edm.String) om strängen representerar en JSON-matris med strängar:`["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |Endast geografiinstanser av typen POINT med SRID 4326 (som är standard) stöds |
| Rowversion |Ej tillämpligt |Kolumner med radversioner kan inte lagras i sökindexet, men de kan användas för ändringsspårning |
| tid, tidsspanna, binär, varbinär, bild, xml, geometri, CLR-typer |Ej tillämpligt |Stöds inte |

## <a name="configuration-settings"></a>Konfigurationsinställningar
SQL-indexeraren visar flera konfigurationsinställningar:

| Inställning | Datatyp | Syfte | Standardvärde |
| --- | --- | --- | --- |
| frågaTimeout |sträng |Anger tidsgränsen för SQL-frågekörning |5 minuter ("00:05:00") |
| inaktiveraOrderByHighWaterMarkColumn |bool |Gör att SQL-frågan som används av principen för högvattenmärke utelämnar ORDER BY-satsen. Se [High Water Mark politik](#HighWaterMarkPolicy) |false |

Dessa inställningar används `parameters.configuration` i objektet i indexeringsdefinitionen. Om du till exempel vill ange tidsgränsen för frågan till 10 minuter skapar eller uppdaterar du indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: Kan jag använda Azure SQL indexer med SQL-databaser som körs på virtuella IaaS-datorer i Azure?**

Ja. Du måste dock tillåta att söktjänsten ansluter till databasen. Mer information finns i [Konfigurera en anslutning från en Azure Cognitive Search-indexerare till SQL Server på en Virtuell Azure.](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

**F: Kan jag använda Azure SQL-indexerare med SQL-databaser som körs lokalt?**

Inte direkt. Vi rekommenderar inte eller stöder ingen direkt anslutning, eftersom det skulle kräva att du öppnar dina databaser för Internet-trafik. Kunder har lyckats med det här scenariot med hjälp av bryggtekniker som Azure Data Factory. Mer information finns i [Push-data till ett Azure Cognitive Search-index med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**F: Kan jag använda Azure SQL-indexerare med andra databaser än SQL Server som körs i IaaS på Azure?**

Nej. Vi stöder inte det här scenariot, eftersom vi inte har testat indexeraren med några andra databaser än SQL Server.  

**F: Kan jag skapa flera indexerare som körs enligt ett schema?**

Ja. Det är dock bara en indexerare som kan köras på en nod samtidigt. Om du behöver flera indexerare som körs samtidigt kan du överväga att skala upp söktjänsten till mer än en sökenhet.

**F: Påverkar körandet av en indexerare min frågearbetsbelastning?**

Ja. Indexer körs på en av noderna i söktjänsten och nodens resurser delas mellan indexering och servering av frågetrafik och andra API-begäranden. Om du kör intensiva indexerings- och frågearbetsbelastningar och stöter på en hög hastighet på 503 fel eller ökande svarstider kan du överväga [att skala upp söktjänsten](search-capacity-planning.md).

**F: Kan jag använda en sekundär replik i ett [redundanskluster](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) som datakälla?**

Det beror på. Om du vill ha fullständig indexering av en tabell eller vy kan du använda en sekundär replik. 

För inkrementell indexering stöder Azure Cognitive Search två principer för ändringsidentifiering: SQL-integrerad ändringsspårning och High Water Mark.

På skrivskyddade repliker stöder SQL-databasen inte integrerad ändringsspårning. Därför måste du använda principen Högvattenmärke. 

Vår standardrekommendation är att använda datatypen rowversion för högvattenmärkespelaren. Men med hjälp av rowversion förlitar sig på SQL Database `MIN_ACTIVE_ROWVERSION` funktion, som inte stöds på skrivskyddade repliker. Därför måste du peka indexeraren på en primär replik om du använder rowversion.

Om du försöker använda rowversion på en skrivskyddad replik visas följande fel: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**F: Kan jag använda en alternativ, icke-rowversion kolumn för hög vatten märke förändring spårning?**

Det rekommenderas inte. Endast **rowversion** möjliggör tillförlitlig datasynkronisering. Beroende på din programlogik kan det dock vara säkert om:

+ Du kan se till att när indexeraren körs finns det inga utestående transaktioner i tabellen som indexeras (till exempel sker alla tabelluppdateringar som en batch enligt ett schema och Azure Cognitive Search-indexerarschemat är inställt för att undvika överlappning med tabellen uppdateringsschema).  

+ Du gör regelbundet en fullständig omindex för att plocka upp missade rader. 