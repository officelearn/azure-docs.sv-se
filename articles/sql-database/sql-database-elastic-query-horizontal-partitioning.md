---
title: "Rapportering över databaser som skalats ut molntjänster | Microsoft Docs"
description: "hur du ställer in elastisk frågor via horisontella partitioner"
services: sql-database
documentationcenter: 
manager: jhubbard
author: MladjoA
ms.assetid: f86eccb8-6323-4ba7-8559-8a7c039049f3
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: mlandzic
ms.openlocfilehash: 62b5bcd26aa1ed219fb38970916e0e8847ceb577
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportering över databaser som skalats ut molnet (förhandsgranskning)
![Fråga på shards][1]

Delat databaser distribuera rader i en skaländras ut data nivå. Schemat är identiska för alla deltagande databaser, även kallat horisontell partitionering. Du kan skapa rapporter som omfattar alla databaser i en delat databas med en elastisk fråga.

En Snabbstart Se [rapportering över databaser som skalats ut molnet](sql-database-elastic-query-getting-started.md).

Icke-delat databaser finns [fråga över moln databaser med olika scheman](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Krav
* Skapa en Fragmentera karta med hjälp av klientbiblioteket för elastisk databas. Se [Fragmentera kartan management](sql-database-elastic-scale-shard-map-management.md). Eller Använd exempelapp i [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* Du kan också se [migrera befintliga databaser som skalats ut databaser](sql-database-elastic-convert-to-use-elastic-tools.md).
* Användaren måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten har behörigheten ALTER DATABASE.
* ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt
De här uttrycken skapa metadata-representation av shardade data-tier i elastisk fråga databas. 

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASBEGRÄNSADE AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Skapa huvudnyckel för databasen omfång och autentiseringsuppgifter
Autentiseringsuppgifterna används av elastisk frågan för att ansluta till din fjärranslutna databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Se till att den *”\<användarnamn\>”* innehåller inte några *”@servername”* suffix. 
> 
> 

## <a name="12-create-external-data-sources"></a>1.2 Skapa externa datakällor
Syntax:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                              
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exempel
    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );

Hämta listan över aktuella externa datakällor: 

    select * from sys.external_data_sources; 

Den externa datakällan refererar till Fragmentera kartan. En elastisk fråga använder sedan den externa datakällan och underliggande Fragmentera kartan att räkna upp de databaser som ingår i datanivå. Samma autentiseringsuppgifter används för att läsa Fragmentera kartan och komma åt data på shards under bearbetning av en elastisk fråga. 

## <a name="13-create-external-tables"></a>1.3 skapa externa tabeller
Syntax:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exempel**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 

    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
         SCHEMA_NAME = 'orders', 
         OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Hämta listan med externa tabeller från databasen: 

    SELECT * from sys.external_tables; 

Att släppa externa tabeller:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Kommentarer
DATA\_SOURCE-satsen definierar den externa datakällan (en Fragmentera karta) som används för den externa tabellen.  

SCHEMAT\_namn och OBJEKTET\_namn satser mappa den externa tabelldefinitionen till en tabell i ett annat schema. Om det utelämnas används schemat för fjärrobjektet antas vara ”dbo” och dess namn antas vara identiskt med extern tabell som definieras. Detta är användbart om namnet på din fjärrtabell används redan i databasen där du vill skapa extern tabell. Till exempel du vill definiera en extern tabell för att få en aggregerad vy över katalogvyer eller nivån av DMV: er på dina data som skalats ut. Eftersom katalogvyer och av DMV: er redan finns lokalt och kan inte du använda deras namn för den externa tabelldefinitionen. I stället använda ett annat namn och använda katalogvyn eller DMV i schemat\_namn och/eller OBJEKTET\_namn-satser. (Se exemplet nedan.) 

Instruktionen DISTRIBUTION anger fördelning data används för den här tabellen. Frågeprocessorn använder informationen i DISTRIBUTION-sats för att skapa de mest effektiva frågeplanerna.  

1. **DELAT** innebär data vågrätt partitionerad över databaser. Partitionsnyckel för Datadistribution är den **< sharding_column_name >** parameter.
2. **REPLIKERADE** innebär att identiska kopior av tabellen finns på varje databas. Det är ditt ansvar att se till att replikerna är identiska mellan databaser.
3. **AVRUNDAR\_ROBIN** innebär att tabellen vågrätt är partitionerad med hjälp av en distributionsmetod för beroende program. 

**Data tjänstnivån referens**: den externa tabellen DDL refererar till en extern datakälla. Den externa datakällan anger en Fragmentera karta som ger den externa tabellen med informationen som behövs för att hitta alla databaser i din datanivå. 

### <a name="security-considerations"></a>Säkerhetsöverväganden
Användare med åtkomst till extern tabell få automatiskt åtkomst till de underliggande fjärrtabeller under autentiseringen i definitionen av externa datakällan. Undvik oönskad höjning av privilegier genom autentiseringsuppgifterna för den externa datakällan. Använd GRANT eller REVOKE för en extern tabell precis som om det vore en vanlig tabell.  

När du har definierat den externa datakällan och externa tabeller kan använda du nu fullständig T-SQL via externa tabeller.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exempel: frågar vågräta partitionerade databaser
Följande fråga utför en trevägs koppling mellan lager order rader, och och använder flera aggregeringar och selektiv filter. Det förutsätts att (1) horisontell partitionering (delning) och (2) att lager order rader, och är delat av datalager ID-kolumnen och att elastisk frågan kan samordna relaterade kopplingar på shards och bearbeta dyra delen av frågan på shards i parallellt. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Lagrade proceduren för fjärrkörning av T-SQL: sp\_execute_remote
Elastisk frågan introducerar också en lagrad procedur som ger direktåtkomst till shards. Den lagrade proceduren anropas [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) och kan användas för att köra fjärråtkomst lagrade procedurer eller T-SQL-kod på den fjärranslutna databaser. Den använder följande parametrar: 

* Namn på datakälla (nvarchar): namnet på den externa datakällan av typen RDBMS. 
* Fråga (nvarchar): T-SQL-frågan ska utföras på varje Fragmentera. 
* Parameterdeklaration (nvarchar) - valfritt: strängen med definitioner av data för de parametrar som används i Frågeparametern (till exempel sp_executesql). 
* Värdet parameterlista - valfritt: kommaavgränsad lista över parametervärden (till exempel sp_executesql).

En sp\_köra\_remote använder den externa datakällan i startparametrar för att köra den angivna T-SQL-instruktionen på fjärr-databaser. Autentiseringsuppgifterna för den externa datakällan används för att ansluta till shardmap manager-databasen och de fjärranslutna databaserna.  

Exempel: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Anslutning för verktyg
Använda reguljära anslutningssträngar för SQL Server för att ansluta ditt program din integreringsverktyg för BI och till databasen med definitionerna extern tabell. Kontrollera att SQL Server stöds som en datakälla för verktyget du behöver. Sedan referens elastisk fråga-databas som andra SQL Server-databas ansluten till verktyget och Använd externa tabeller från verktyget eller program som om de vore lokala tabeller. 

## <a name="best-practices"></a>Bästa praxis
* Se till att elastisk fråga endpoint databas har behörighet till shardmap databasen och alla delar genom brandväggar för SQL-databas.  
* Validera eller tillämpa Datadistribution som definieras av den externa tabellen. Om din distribution för faktiska data skiljer sig från distributionsplatsen som anges i din tabelldefinitionen kan dina frågor ge oväntade resultat. 
* Elastisk frågan för närvarande inte att utföra Fragmentera eliminering när predikat över nyckeln för horisontell partitionering skulle göra det möjligt att på ett säkert sätt utesluta vissa delar bearbetning.
* Elastisk frågan fungerar bäst för frågor där de flesta av beräkningen kan göras på shards. Det uppstår vanligtvis bästa frågeprestanda med selektiv filter-predikat som kan utvärderas på shards eller kopplingar över partitionering nycklar som kan utföras på ett partitionsjusterade sätt på alla delar. Andra frågemönster kan behöva läsa in stora mängder data från delar till huvudnod och kan fungera dåligt

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk frågan översikt](sql-database-elastic-query-overview.md).
* Vertikal partitionering, finns [komma igång med flera databaser fråga (vertikal partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade finns [frågar lodrätt partitionerad data)](sql-database-elastic-query-vertical-partitioning.md)
* Horisontell partitionering (delning), finns [komma igång med elastisk frågan för horisontell partitionering (delning)](sql-database-elastic-query-getting-started.md).
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktion på en enda remote Azure SQL Database eller en uppsättning databaser som fungerar som delar i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
