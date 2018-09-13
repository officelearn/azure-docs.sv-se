---
title: Ansluta Azure SQL-databas till Azure Search med indexerare | Microsoft Docs
description: Lär dig hur du hämtar data från Azure SQL Database till ett Azure Search-index med hjälp av indexerare.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 5897740a1b5a183738c08b4dfde571be652aff3e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650340"
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Ansluta Azure SQL Database till Azure Search med indexerare

Innan du kan fråga efter en [Azure Search-index](search-what-is-an-index.md), måste du fylla den med dina data. Om dessa data finns i en Azure SQL-databas och en **Azure Search-indexeraren för Azure SQL Database** (eller **Azure SQL-indexeraren** för kort) kan automatisera indexeringsprocessen, vilket innebär att mindre kod att skriva och mindre infrastruktur intresserar dig.

Den här artikeln beskriver säkerhetsnivån med [indexerare](search-indexer-overview.md), men även beskriver funktionerna som är endast tillgänglig med Azure SQL-databaser (till exempel integrerad ändringsspårning). 

Utöver Azure SQL-databaser, Azure Search innehåller indexerare för [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob storage](search-howto-indexing-azure-blob-storage.md), och [Azure-tabellagring](search-howto-indexing-azure-tables.md). Att begära stöd för andra datakällor, ge feedback på den [feedback-forum för Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexerare och datakällor

En **datakälla** anger vilka data du index, autentiseringsuppgifter för åtkomst till data och principer som effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). Den har definierats som en oberoende resurs så att den kan användas av flera indexerare.

En **indexeraren** är en resurs som ansluter en enskild datakälla med en riktad search-index. En indexerare används på följande sätt:

* Utföra en enstaka kopia av data kan fylla ett index.
* Uppdatera ett index med ändringar i datakällan enligt ett schema.
* Kör på begäran för att uppdatera ett index efter behov.

En enda indexerare kan bara använda en tabell eller vy, men du kan skapa flera indexerare om du vill fylla i flera search-index. Mer information om begrepp finns i [Indexeru: typiskt arbetsflöde](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Du kan ställa in och konfigurera en Azure SQL-indexeraren med:

* Guiden Importera Data i den [Azure-portalen](https://portal.azure.com)
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

I den här artikeln använder vi REST-API för att skapa **indexerare** och **datakällor**.

## <a name="when-to-use-azure-sql-indexer"></a>När du ska använda Azure SQL-indexeraren
Beroende på flera faktorer som är relaterade till dina data, användning av Azure SQL-indexeraren kanske eller kanske inte är lämpliga. Om dina data passar följande krav, kan du använda Azure SQL-indexeraren.

| Kriterie | Information |
|----------|---------|
| Data samlas in från en enda tabell eller vy | Om data är spridda över flera tabeller, kan du skapa en vy av data. Om du använder en vy kan att du inte kunna använda identifiering av ändring av SQL Server som är integrerade för att uppdatera ett index med inkrementella ändringar. Mer information finns i [samla in ändras och ta bort rader](#CaptureChangedRows) nedan. |
| Datatyperna är kompatibla | De flesta men inte alla SQL typer stöds i ett Azure Search-index. En lista i [mappning datatyper](#TypeMapping). |
| Synkronisering av data i realtid krävs inte | En indexerare kan indexera tabellen högst var femte minut. Om data ändras ofta och ändringarna måste återspeglas i indexet inom några sekunder eller enskild minuter, bör du använda den [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) eller [.NET SDK](search-import-data-dotnet.md) att skicka uppdaterade rader direkt. |
| Inkrementell indexering är möjligt | Om du har en stor datauppsättning och tänker köra indexeraren enligt ett schema kan måste Azure Search kunna effektivt identifiera nya, ändrade eller borttagna rader. Icke-inkrementell indexering tillåts endast om du är indexering på begäran (inte enligt schema) eller indexering färre än 100 000 rader. Mer information finns i [samla in ändras och ta bort rader](#CaptureChangedRows) nedan. |

> [!NOTE] 
> Azure Search har stöd för SQL Server-autentisering. Om du behöver support för Azure Active Directory-lösenordsautentisering rösta för den här [UserVoice förslag](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Skapa en Azure SQL-indexerare

1. Skapa datakällan:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Du kan hämta anslutningssträngen från den [Azure-portalen](https://portal.azure.com); Använd det `ADO.NET connection string` alternativet.

2. Skapa mål Azure Search-index om du inte redan har en. Du kan skapa ett index med hjälp av den [portal](https://portal.azure.com) eller [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Kontrollera att schemat för din målindex är kompatibel med schemat för källtabellen – Se [mappning mellan SQL och Azure search-datatyper](#TypeMapping).

3. Skapa indexeraren genom att ge den ett namn och refererar till dataIndex för källa och mål:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

En indexerare som skapats på det här sättet har inte ett schema. Det körs automatiskt när när den har skapats. Du kan köra den igen när som helst med en **köra indexeraren** begäran:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

Du kan anpassa flera aspekter av indexeraren fungerar, till exempel batchstorlek och hur många dokument kan du hoppa över innan det går inte att köra en indexerare. Mer information finns i [skapa Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Du kan behöva ge Azure-tjänster att ansluta till databasen. Se [ansluta från Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för anvisningar om hur du gör.

Du övervakar indexeraren status och körning historiken (antal objekt som indexeras, fel, etc.) med en **indexeraren status** begäran:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
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

Körningshistorik innehåller upp till 50 i den nyligen utförda körningar, sorteras i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).
Mer information om svaret finns i [hämta Status för indexerare](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Kör indexerare enligt ett schema
Du kan även ordna indexeraren ska köras regelbundet enligt ett schema. Gör detta genom att lägga till den **schema** egenskapen när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en PUT-begäran för att uppdatera indexeraren:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Den **intervall** parametern är obligatorisk. Intervallet avser tiden mellan början av två på varandra följande indexeraren körningar. Minsta tillåtna intervall är 5 minuter. den längsta är en dag. Den måste vara formaterad som en XSD-värde för ”dayTimeDuration” (en begränsad delmängd av en [varaktighet i ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värde). Mönstret för det här är: `P(nD)(T(nH)(nM))`. Exempel: `PT15M` för varje kvart `PT2H` för varannan timme.

Den valfria **startTime** anger när den schemalagda körningar ska påbörjas. Om det utelämnas används den aktuella UTC-tiden. Nu kan vara i det förflutna – om den första körningen har schemalagts som om indexeraren har körts sedan startTime oavbrutet.  

Endast en körning av en indexerare kan köras i taget. Om en indexerare körs när körningen har schemalagts, är körningen skjutas upp tills nästa schemalagda tid.

Vi tar ett exempel för att göra detta mer konkret. Anta att vi följande timschema som konfigurerats:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Här är vad som händer:

1. Den första körningen av indexeraren startar vid eller runt den 1 mars 2015 klockan 12:00 UTC.
2. Anta att den här körningen tar 20 minuter (eller när som helst mindre än 1 timme).
3. Den andra körningen startar vid eller runt den 1 mars 2015 klockan 1:00
4. Anta nu att den här körningen tar mer än en timme – till exempel 70: e minut – så att den är klar ungefär kl 02:10:00
5. Det är nu kl, tid att starta tredje körningen. Men eftersom den andra körningen från 1 a.m. körs fortfarande, tredje körningen har hoppats över. Tredje körningen startar klockan 03

Du kan lägga till, ändra eller ta bort ett schema för en befintlig indexerare med hjälp av en **PUT indexeraren** begäran.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Samla in nya, ändrade och borttagna rader

Använder Azure Search **inkrementella indexering** för att förhindra att indexera hela tabellen eller vyn varje gång körs en indexerare. Azure Search har två ändra principer för att stödja inkrementella indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL-integrerad ändringsspårning princip
Om din SQL database stöder [ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), bör du använda **SQL integrerad ändra spåra Policy**. Det här är den mest effektiva principen. Dessutom kan Azure Search för att identifiera borttagna rader utan att du behöver lägga till en explicit ”mjuk borttagning”-kolumnen i tabellen.

#### <a name="requirements"></a>Krav 

+ Databaskrav version:
  * SQL Server 2012 SP3 och senare, om du använder SQL Server på Azure Virtual Machines.
  * Azure SQL Database V12 om du använder Azure SQL Database.
+ Tabeller endast (inga vyer). 
+ I databasen [Aktivera ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) för tabellen. 
+ Ingen sammansatt primär nyckel (en primär nyckel som innehåller mer än en kolumn) i tabellen.  

#### <a name="usage"></a>Användning

Om du vill använda den här principen, skapa eller uppdatera din datakälla Så här:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Använda SQL-integrerad ändringsspårning princip, ange inte en separat princip för identifiering av borttagning – den här principen har inbyggt stöd för att identifiera när borttagna rader. Men för borttagningar ska identifierade ”automagically” måste dokumentnyckeln i dina search-index vara samma som den primära nyckeln i SQL-tabell. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Princip för identifiering av ändring av vattenmärke

Princip för den här ändringen är beroende av en kolumn för ”vattenmärke” samla in version eller tid när en rad uppdaterades senast. Om du använder en vy, måste du använda en princip för vattenmärke. Kolumnen vattenmärke måste uppfylla följande krav.

#### <a name="requirements"></a>Krav 

* Alla infogningar ange ett värde för kolumnen.
* Alla uppdateringar till ett objekt kan också ändra värdet för kolumnen.
* Värdet för den här kolumnen ökar med varje insert eller update.
* Frågor med följande där och ORDER BY-satser kan köras effektivt: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Vi rekommenderar starkt med hjälp av den [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) datatypen för kolumnen vattenmärke. Om någon annan datatyp används är för att spåra ändringar inte säkert att samla in alla ändringar när det finns transaktioner som körs samtidigt med en indexerare-fråga. När du använder **rowversion** i en konfiguration med skrivskyddade repliker, måste du peka indexeraren på den primära repliken. Endast en primär replik kan användas för data sync scenarier.

#### <a name="usage"></a>Användning

Om du vill använda en princip för vattenmärke för att skapa eller uppdatera din datakälla Så här:

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
> Om källtabellen inte har ett index i kolumnen vattenmärke kan frågorna som används av SQL-indexeraren timeout. I synnerhet de `ORDER BY [High Water Mark Column]` satsen kräver ett index att effektivt köra när tabellen innehåller många rader.
>
>

Om du stöter på timeout-fel kan du använda den `queryTimeout` indexeraren konfigurationsinställning för att ange timeout-värde till ett högre värde än standardvärdet 5 minuter för timeout. Till exempel om du vill ange timeout-värdet till 10 minuter, skapa eller uppdatera indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Du kan också inaktivera den `ORDER BY [High Water Mark Column]` satsen. Men rekommenderas detta inte eftersom om indexerare-körningen har avbrutits av ett fel kan indexeraren måste igen bearbeta alla rader om det senare – körs även om indexeraren har redan bearbetats nästan alla rader när den avbröts. Så här inaktiverar du den `ORDER BY` -satsen, Använd den `disableOrderByHighWaterMarkColumn` i att indexerarens definition:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Ej permanent ta bort kolumnen borttagning av princip
När rader har tagits bort från källtabellen, vill du förmodligen ta bort de raderna från search-index. Om du använder principen SQL-integrerad ändringsspårning kan sköts detta av åt dig. Dock hjälpa princip vattenmärke för ändringsspårning inte dig med borttagna rader. Hur ska du göra?

Om raderna tas bort fysiskt från tabellen, har inget sätt att härleda förekomsten av poster som inte längre finns i Azure Search.  Du kan dock använda ”mjuk borttagning” metod för att logiskt ta bort rader utan att ta bort dem från tabellen. Lägg till en kolumn till din tabell eller vy och markera rader som tas bort med hjälp av kolumnen.

När du använder mjuk borttagning-teknik kan ange du det princip för mjukt borttagning på följande sätt när du skapar eller uppdaterar datakällan:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

Den **softDeleteMarkerValue** måste vara en sträng – Använd den sträng som innehåller din faktiskt värde. Till exempel om du har en heltalskolumn där borttagna rader har markerats med värdet 1, använda `"1"`. Om du har en BIT-kolumn där borttagna rader har markerats med det booleska värdet true, Använd `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mappning mellan SQL och Azure Search-datatyper
| SQL-datatypen | Tillåtna målindex fälttyper | Anteckningar |
| --- | --- | --- |
| bitars |Edm.Boolean Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64 Edm.String | |
| verkliga, flyttal |Edm.Double Edm.String | |
| smallmoney, pengar decimal numeriskt |Edm.String |Azure Search har inte stöd för konvertering decimal typer i Edm.Double eftersom detta skulle förlora precision |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |En SQL-strängen kan användas för att fylla i ett fält för Collection(Edm.String) om strängen representerar en JSON-matris med strängar: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset Edm.String | |
| uniqueidentifer |Edm.String | |
| Geografisk plats |Edm.GeographyPoint |Stöds endast geografi instanser av typen punkt med SRID 4326 (som är standard) |
| ROWVERSION |Gäller inte |Rad-version kolumner kan inte lagras i sökindexet, men de kan användas för ändringsspårning |
| tid, tidsintervall, binary, varbinary, bild, xml, geometri, CLR-typer |Gäller inte |Stöds inte |

## <a name="configuration-settings"></a>Konfigurationsinställningar
SQL-indexeraren visar flera konfigurationsinställningar:

| Inställning | Datatyp | Syfte | Standardvärde |
| --- | --- | --- | --- |
| queryTimeout |sträng |Anger timeout för SQL-frågekörning |5 minuter (”00: 05:00”) |
| disableOrderByHighWaterMarkColumn |Bool |Gör att SQL-fråga som används av vattenmärke för principen för att utelämna ORDER BY-satsen. Se [vattenmärke för principen](#HighWaterMarkPolicy) |false |

De här inställningarna används i den `parameters.configuration` objekt i att indexerarens definition. Till exempel om du vill ange timeout-värde till 10 minuter, skapa eller uppdatera indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: kan jag använda Azure SQL-indexeraren med SQL-databaser som körs på virtuella IaaS-datorer i Azure?**

Ja. Du måste dock tillåta söktjänsten att ansluta till databasen. Mer information finns i [konfigurera en anslutning från en Azure Search-indexerare till SQL Server på en Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**F: kan jag använda Azure SQL-indexeraren med SQL-databaser som körs lokalt?**

Inte direkt. Vi rekommenderar att du eller inte stöder en direktanslutning enligt detta så kräver du att öppna dina databaser till Internet-trafik. Kunder har lyckades med det här scenariot med brygga tekniker som Azure Data Factory. Mer information finns i [skicka data till ett Azure Search-index med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**F: kan jag använda Azure SQL-indexeraren med databaser än SQL Server som körs i IaaS på Azure?**

Nej. Vi stöder inte det här scenariot eftersom vi inte har testat indexerare med databaser än SQL Server.  

**F: kan jag skapa flera indexerare som körs enligt ett schema?**

Ja. Men kan bara en indexerare köras på en nod i taget. Om du behöver flera indexerare som körs samtidigt kan du överväga att skala upp din söktjänst till mer än en sökenheten.

**F: kan köra en indexerare påverkar arbetsbelastningens fråga?**

Ja. Indexeraren körs på en av noderna i din söktjänst och den noden resurser delas mellan indexering och betjänar fråga trafik och andra API-begäranden. Om du kör beräkningsintensiva indexerings- och belastningar och stöter på en hög andel 503-fel eller ökade svarstider, bör du [proportionellt söktjänsten](search-capacity-planning.md).

**F: kan jag använda en sekundär replik i en [redundanskluster](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) som en datakälla?**

Det beror på. Du kan använda en sekundär replik för fullständig indexering för en tabell eller vy. 

Azure Search har stöd för två ändra principer för inkrementell indexering: SQL integrerade ändra spårning och hög vattenmärke.

SQL database stöder inte integrerad ändringsspårning på skrivskyddade repliker. Därför måste du använda vattenmärke för principen. 

Vår standard rekommendation är att använda rowversion datatypen för kolumnen vattenmärke. Men med rowversion förlitar sig på SQL-databas `MIN_ACTIVE_ROWVERSION` som inte stöds på skrivskyddade repliker. Därför måste du peka indexeraren på en primär replik om du använder rowversion.

Om du försöker använda rowversion på en skrivskyddad replik visas följande fel: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**F: kan jag använda en alternativ, icke-rowversion kolumn för vattenmärke för ändringsspårning**

Det rekommenderas inte. Endast **rowversion** möjliggör tillförlitlig datasynkronisering. Beroende på programlogiken, det kan dock vara säkra om:

+ Du kan se till att när indexeraren körs det inte finns några väntande transaktioner på den tabell som indexeras (till exempel alla tabelluppdateringar sker som en batch enligt ett schema och schema för Azure Search-indexeraren är inställd på att undvika överlappande med uppdatera tabell schema).  

+ Du göra regelbundet en fullständig reindex att hämta alla saknade rader. 
