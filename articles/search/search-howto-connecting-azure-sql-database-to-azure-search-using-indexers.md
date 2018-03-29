---
title: Ansluta Azure SQL-databas till Azure sökning med hjälp av indexerare | Microsoft Docs
description: Lär dig hur du hämtar data från Azure SQL Database till ett Azure Search-index med hjälp av indexerare.
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/26/2018
ms.author: eugenesh
ms.openlocfilehash: a5198cc6e3b019fc6fd241f22c4da088f0839066
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Ansluta Azure SQL Database till Azure Search med indexerare

Innan du kan fråga en [Azure Search index](search-what-is-an-index.md), måste du fylla den med dina data. Om data finns i en Azure SQL database en **Azure Search indexerare för Azure SQL Database** (eller **Azure SQL indexeraren** för kort) kan automatisera indexering processen, vilket innebär att mindre kod för att skriva och mindre infrastruktur som intresserar dig.

Den här artikeln omfattar säkerhetsnivån med [indexerare](search-indexer-overview.md), men även beskriver funktionerna kan endast användas med Azure SQL-databaser (till exempel integrerad ändringsspårning). 

Utöver Azure SQL-databaser innehåller Azure Search indexerare för [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob storage](search-howto-indexing-azure-blob-storage.md), och [Azure-tabellagring](search-howto-indexing-azure-tables.md). Om du vill begära stöd för andra datakällor ge din feedback på den [Azure Search Feedbackforum](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexerare och datakällor

En **datakällan** anger vilka data som ska index, autentiseringsuppgifter för åtkomst till data och principer som effektivt identifiera ändringar i data (nya, ändrade eller borttagna rader). Den har definierats som en oberoende resurs så att den kan användas av flera indexerare.

En **indexeraren** är en resurs som ansluter en enskild datakälla med ett riktade search-index. En indexerare används på följande sätt:

* Utför en enstaka kopia av data att fylla i ett index.
* Uppdatera ett index med ändringar i datakällan enligt ett schema.
* Kör på begäran för att uppdatera ett index efter behov.

En enda indexeraren kan bara använda en tabell eller vy, men du kan skapa flera indexerare om du vill fylla i flera search-index. Mer information om begrepp finns [Indexeringsåtgärder: vanliga arbetsflöde](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Du kan installera och konfigurera en Azure SQL indexeraren med:

* Guiden Importera Data i den [Azure-portalen](https://portal.azure.com)
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Search [REST-API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

I den här artikeln använder vi REST API för att skapa **indexerare** och **datakällor**.

## <a name="when-to-use-azure-sql-indexer"></a>När du använder Azure SQL indexeraren
Beroende på flera faktorer som är relaterade till dina data, användning av Azure SQL indexeraren kanske eller kanske inte är rätt. Om dina data uppfyller följande krav kan använda du SQL Azure indexeraren.

| Kriterie | Information |
|----------|---------|
| Data har sitt ursprung i en enda tabell eller vy | Om data är spridda över flera tabeller, kan du skapa en enda vy av data. Om du använder en vy, kan du inte kommer att kunna Använd identifiering av ändring av SQL Server integrerad för att uppdatera ett index med stegvisa ändringar. Mer information finns i [fånga ändras och ta bort rader](#CaptureChangedRows) nedan. |
| Datatyperna är kompatibla | De flesta, men inte alla SQL typer stöds i ett Azure Search-index. En lista, se [mappning datatyper](#TypeMapping). |
| Realtidsdata synkronisering krävs inte | En indexerare kan indexera tabellen högst var femte minut. Om data ändras ofta och ändringarna måste återspeglas i indexet inom några sekunder eller minuter som enda, bör du använda den [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) eller [.NET SDK](search-import-data-dotnet.md) att skicka uppdaterade rader direkt. |
| Det är möjligt att stegvis indexering | Om du har stora datamängder och planerar att köra indexeraren enligt ett schema, måste Azure Search vara ett effektivt sätt identifiera nya, ändrade eller borttagna rader. Icke-inkrementell indexering tillåts endast om du indexering på begäran (inte på schema), eller indexering färre än 100 000 rader. Mer information finns i [fånga ändras och ta bort rader](#CaptureChangedRows) nedan. |

> [!NOTE] 
> Azure Search har stöd för SQL Server-autentisering. Om du behöver support för Azure Active Directory-lösenordsautentisering rösta för den här [UserVoice förslag](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Skapa en Azure SQL-indexerare

1. Skapa datakällan:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Du kan hämta anslutningssträngen från den [Azure-portalen](https://portal.azure.com); använder den `ADO.NET connection string` alternativet.

2. Skapa mål Azure Search index om du inte redan har en. Du kan skapa ett index med hjälp av den [portal](https://portal.azure.com) eller [skapa Index API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Se till att schemat för mål-index är kompatibel med schemat för källtabellen - Se [mappning mellan SQL och Azure search-datatyper](#TypeMapping).

3. Skapa indexeraren genom att ge det ett namn och refererar till käll- och index för data:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

En indexerare som skapas på detta sätt har inte ett schema. Det körs automatiskt när när den har skapats. Du kan köra det igen på en gång med hjälp av en **köra indexeraren** begäran:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Du kan anpassa flera aspekter av indexerare beteende, t.ex batchstorlek och hur många dokument kan hoppas över innan det går inte att köra en indexerare. Mer information finns i [skapa indexeraren API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Du kan behöva ge Azure-tjänster att ansluta till databasen. Se [ansluta från Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) instruktioner om hur du gör.

Om du vill övervaka indexeraren status och körningen historik (antal objekt som indexeras, fel, etc.) använder en **indexeraren status** begäran:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

Svaret bör se ut ungefär så här:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
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

Körningstiden innehåller upp till 50 i nyligen utförda körningar, vilket är sorterade i omvänd kronologisk ordning (så att den senaste körningen kommer först i svaret).
Mer information om svaret kan hittas i [Erhåll Status för indexerare](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Indexerare körs enligt ett schema
Du kan även ordna indexeraren ska köras regelbundet enligt ett schema. Det gör du genom att lägga till den **schema** egenskapen när du skapar eller uppdaterar indexeraren. Exemplet nedan visar en PUT-förfrågan för att uppdatera indexeraren:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Den **intervall** parametern är obligatorisk. Intervallet avser tiden mellan starten av två på varandra följande indexeraren körningar. Minsta tillåtna intervall är 5 minuter. den längsta är en dag. Den måste formateras som ett XSD ”daytimeduration” XSD-värde (en begränsad delmängd av ett [ISO 8601 varaktighet](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värdet). Mönstret för detta är: `P(nD)(T(nH)(nM))`. Exempel: `PT15M` för varje kvart `PT2H` för varannan timme.

Det valfria **startTime** anger när schemalagda körningar ska börja. Om det utelämnas används den aktuella UTC-tid. Nu kan vara i det förflutna – fallet första körningen har schemalagts som om indexeraren har körts sedan startTime oavbrutet.  

Endast en körning av en indexerare kan köras samtidigt. Om en indexerare körs när körningen har schemalagts skjuts körningen tills nästa schemalagda tid.

Nu ska vi titta ett exempel för att göra det mer konkreta. Anta att vi följande timschema som konfigurerats:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Här är vad som händer:

1. Den första körningen indexeraren startar på eller runt den 1 mars 2015 klockan 12:00 UTC.
2. Anta att den här körningen tar 20 minuter (eller när mindre än 1 timme).
3. Andra körningen startar på eller runt den 1 mars 2015 klockan 1:00
4. Anta nu att denna tar mer än en timme – till exempel 70: e minut – så att den är klar cirka 2 10 klockan:
5. Det är nu. 02:00, tid för att starta tredje köra. Men eftersom andra körningen från 1 kl. är fortfarande körs tredje körningen hoppas över. Tredje körningen startar klockan 03

Du kan lägga till, ändra eller ta bort ett schema för en befintlig indexerare med hjälp av en **PUT indexeraren** begäran.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Samla in nya, ändrade och borttagna rader

Azure Search använder **inkrementell indexering** för att undvika att indexera hela tabellen eller vyn varje gång en indexeraren körs. Azure Search innehåller två ändra principer för att stödja inkrementell indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL-integrerad ändringsspårning princip
Om SQL-databasen stöder [ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), bör du använda **SQL integrerad ändra Spårningsprincip**. Detta är den mest effektiva principen. Dessutom kan Azure Search att identifiera borttagna rader utan att lägga till en explicit ”mjuk borttagning”-kolumn i tabellen.

#### <a name="requirements"></a>Krav 

+ Databaskrav version:
  * SQL Server 2012 SP3 och senare, om du använder SQL Server på Azure Virtual Machines.
  * Azure SQL Database V12, om du använder Azure SQL Database.
+ Tabeller endast (inga vyer). 
+ I databasen [Aktivera ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) för tabellen. 
+ Ingen sammansatt primärnyckel (en primär nyckel som innehåller mer än en kolumn) i tabellen.  

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

Ta bort rader med hjälp av SQL-integrerad ändringsspårning princip, ange inte en separat data princip för identifiering av borttagning - principen har inbyggt stöd för att identifiera när. Men för borttagningar ska identifierade ”automagically” måste Dokumentnyckel i search-index vara samma som den primära nyckeln i SQL-tabellen. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Princip för identifiering av ändring av vattenmärke

Princip för den här ändringen är beroende av ett ”vattenmärke” kolumn fånga version eller tid när en rad uppdaterades senast. Om du använder en vy, måste du använda en princip för vattenmärke. Kolumnen vattenmärke måste uppfylla följande krav.

#### <a name="requirements"></a>Krav 

* Alla infogningar ange ett värde för kolumnen.
* Alla uppdateringar till ett objekt kan du också ändra värdet för kolumnen.
* Värdet för den här kolumnen ökar med varje insert eller update.
* Frågor med följande där och ORDER BY-satser kan köras effektivt: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Vi rekommenderar starkt med hjälp av den [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) datatypen för kolumnen vattenmärke. Om-datatypen används är ändringsspårning inte säkert att samla in alla ändringar med transaktioner som körs samtidigt med en indexerare fråga. När du använder **rowversion** i en konfiguration med skrivskyddade repliker, måste du peka indexeraren på den primära repliken. Endast en primär replik kan användas för scenarier för synkronisering av data.

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
> Om källtabellen inte har ett index för kolumnen vattenmärke för får frågor som används av SQL-indexeraren timeout. I synnerhet de `ORDER BY [High Water Mark Column]` satsen kräver ett index ska köras effektivt när tabellen innehåller många rader.
>
>

Om du får timeout-fel, kan du använda den `queryTimeout` indexeraren Konfigurationsinställningen att ange timeout-värde till ett högre värde än standardvärdet 5 minuter för timeout. Om du vill ange tidsgränsen till 10 minuter, till exempel skapa eller uppdatera indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Du kan också inaktivera den `ORDER BY [High Water Mark Column]` satsen. Men rekommenderas detta inte eftersom om indexeraren körningen har avbrutits av ett fel kan indexeraren har nytt bearbeta alla rader om den senare – körs även om indexeraren har redan behandlats nästan alla rader av den tid som den avbröts. Så här inaktiverar du den `ORDER BY` -satsen, Använd den `disableOrderByHighWaterMarkColumn` i indexeraren definition:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Ej permanent ta bort kolumnen ta bort princip
När rader tas bort från källtabellen, vill du förmodligen ta bort dem från sökindexet samt. Om du använder principen SQL-integrerad ändringsspårning har detta åtgärdat för dig. Principen vattenmärke ändringsspårning hjälpa inte dig med borttagna rader. Hur ska du göra?

Om raderna är fysiskt bort från tabellen, har Azure Search inget sätt att härleda förekomst av poster som inte längre finns.  Du kan dock använda ”soft-ta bort” tekniken logiskt ta bort rader utan att ta bort dem från tabellen. Lägg till en kolumn till en tabell eller vy och markera rader som tas bort med hjälp av kolumnen.

När du använder teknik för mjuk borttagning kan ange du det princip för mjukt borttagning på följande sätt när du skapar eller uppdaterar datakällan:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

Den **softDeleteMarkerValue** måste vara en sträng – Använd strängrepresentation av din faktiskt värde. Till exempel om du har en heltalskolumn där borttagna rader har markerats med värdet 1, Använd `"1"`. Om du har en BIT-kolumn där borttagna rader har markerats med booleska true-värde, Använd `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mappning mellan datatyperna SQL och Azure Search
| SQL-datatypen | Tillåtna målindexet fälttyp | Anteckningar |
| --- | --- | --- |
| bitar |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| verkliga, float |Edm.Double, Edm.String | |
| smallmoney pengar decimal numeriskt |Edm.String |Azure Search har inte stöd för konvertering av decimaltyper till Edm.Double eftersom detta skulle förlora precision |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |En SQL-sträng kan användas för att fylla i ett fält för Collection(Edm.String) om strängen som representerar en JSON-matris med strängar: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geografisk plats |Edm.GeographyPoint |Stöds endast geografi instanser av typen plats med SRID 4326 (som är standard) |
| rowversion |Gäller inte |Radversioner kolumner kan inte lagras i sökindexet, men de kan användas för ändringsspårning |
| tid, timespan, binary, varbinary, image, xml, geometry, CLR-typer |Gäller inte |Stöds inte |

## <a name="configuration-settings"></a>Konfigurationsinställningar
SQL-indexeraren visar flera konfigurationsinställningar:

| Inställning | Datatyp | Syfte | Standardvärde |
| --- | --- | --- | --- |
| queryTimeout |sträng |Anger timeout för körning av SQL-fråga |5 minuter (”00: 05:00”) |
| disableOrderByHighWaterMarkColumn |bool |Gör att SQL-fråga som används av vattenmärke för principen för att utelämna ORDER BY-satsen. Se [vattenmärke för principen](#HighWaterMarkPolicy) |false |

De här inställningarna används i den `parameters.configuration` objekt i indexeraren definitionen. Om du vill ange timeout-värde till 10 minuter, till exempel skapa eller uppdatera indexeraren med följande konfiguration:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F: kan jag använda Azure SQL indexeraren med SQL-databaser som körs på virtuella IaaS-datorer i Azure?**

Ja. Du måste dock tillåta search-tjänsten att ansluta till databasen. Mer information finns i [konfigurera en anslutning från en indexerare för Azure Search till SQL Server på en Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**F: kan jag använda Azure SQL indexeraren med SQL-databaser som körs lokalt?**

Inte direkt. Vi rekommenderar eller inte stöd för en direkt anslutning som gör så skulle kräva att öppnas databaserna för Internet-trafiken. Kunder har har slutförts med det här scenariot med bridge tekniker som Azure Data Factory. Mer information finns i [skicka data till en Azure Search-index med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**F: kan jag använda Azure SQL indexeraren med databaser än SQL Server körs i IaaS på Azure?**

Nej. Vi stöder inte det här scenariot eftersom vi inte har testat indexerare med alla databaser än SQL Server.  

**F: kan jag skapa flera indexerare som körs på ett schema?**

Ja. Dock kan bara en indexerare köras på en nod i taget. Överväg att skala upp din söktjänst till mer än en sökning-enhet om du behöver flera indexerare som körs samtidigt.

**F: kan körning påverkar en indexerare min fråga?**

Ja. Indexeraren körs på en av noderna i din söktjänst och den noden resurser delas mellan indexering och hanterar frågetrafik och andra API-begäranden. Om du kör indexering och fråga arbetsbelastningar och påträffar en hög andel 503 fel eller ökande svarstider, bör du [skala upp din söktjänst](search-capacity-planning.md).

**F: kan jag använda en sekundär replik i en [redundanskluster](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) som en datakälla?**

Det beror på. Du kan använda en sekundär replik fullständig indexering av en tabell eller vy. 

Azure Search stöder två ändra principer för inkrementell indexering: SQL-integrerad ändra spårnings- och vattenmärke.

SQL-databasen stöder inte integrerad ändringsspårning på skrivskyddade repliker. Därför måste du använda vattenmärke för principen. 

Vår rekommendation som standard är att använda rowversion datatypen för kolumnen vattenmärke. Men med rowversion bygger på SQL-databas `MIN_ACTIVE_ROWVERSION` funktion, vilket inte stöds på skrivskyddade repliker. Därför måste du peka indexeraren på en primär replik om du använder rowversion.

Om du försöker använda rowversion på en skrivskyddad replik visas följande fel: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**F: kan jag använda en alternativ, icke-rowversion kolumn för vattenmärke för ändringsspårning?**

Det rekommenderas inte. Endast **rowversion** gör det möjligt för tillförlitlig datasynkronisering. Beroende på din programlogik, det kan dock finnas säker om:

+ Du kan kontrollera när indexeraren körs, det finns inga väntande transaktioner på den tabell som indexeras (till exempel alla tabellen hända som en batch på ett schema och schemat för Azure Search-indexeraren är inställd på att undvika överlappande med tabell-uppdateringen schemalägga uppdateringar).  

+ Du göra regelbundet en fullständig omindexera att hämta några saknade rader. 
