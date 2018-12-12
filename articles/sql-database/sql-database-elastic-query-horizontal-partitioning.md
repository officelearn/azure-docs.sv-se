---
title: Rapportering över utskalade molndatabaser | Microsoft Docs
description: hur du ställer in elastiska frågor över horisontella partitioner
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: de96de96d68164d021f8b823e69bc52322642aa7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865411"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportering över utskalade molndatabaser (förhandsversion)
![Fråga över shards][1]

Shardade databaser distribuera rader i en utskalad data nivå. Schemat är identiska på alla deltagande databaser, även kallat horisontell partitionering. Du kan skapa rapporter som sträcker sig över alla databaser i en fragmenterade (sharded) databas med en elastisk fråga.

En Snabbstart Se [rapportering över utskalade molndatabaser](sql-database-elastic-query-getting-started.md).

Icke-shardade databaser finns [fråga över molndatabaser med olika scheman](sql-database-elastic-query-vertical-partitioning.md). 

## <a name="prerequisites"></a>Förutsättningar
* Skapa en skärvkarta med hjälp av klientbiblioteket för elastiska databaser. Se [fragmentkarthantering](sql-database-elastic-scale-shard-map-management.md). Eller Använd exempelprogram i [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* Du kan också se [migrera befintliga databaser till utskalade databaser](sql-database-elastic-convert-to-use-elastic-tools.md).
* Användaren måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten ingår behörigheten ALTER DATABASE.
* ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt
Dessa instruktioner skapa metadata-representation av shardade datanivå i elastisk fråga i databasen. 

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASBEGRÄNSADE AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Skapa databasomfattande huvudnyckel och autentiseringsuppgifter
Autentiseringsuppgifterna används av elastisk fråga för att ansluta till din fjärranslutna databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Se till att den *”\<användarnamn\>”* innehåller inte några *”\@servername”* suffix. 
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

Extern datakälla refererar till din fragmentkartan. En elastic query använder sedan den externa datakällan och den underliggande fragmentkartan att räkna upp de databaser som deltar på datanivå. Samma autentiseringsuppgifter används för att läsa fragmentkartan och ansluta till data i shards under bearbetning av en elastisk förfrågan. 

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

Hämta listan över externa tabeller från databasen: 

    SELECT * from sys.external_tables; 

Att ta bort externa tabeller:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Kommentarer
DATA\_SOURCE-satsen definierar den externa datakällan (en skärvkarta) som används för den externa tabellen.  

SCHEMAT\_namn och OBJEKTET\_namn-satser mappa den externa tabelldefinitionen till en tabell i ett annat schema. Om det utelämnas används schemat för fjärrobjektet antas vara ”dbo” och dess namn antas vara identiskt med namnet för extern tabell som definieras. Detta är användbart om namnet på din fjärrtabellen används redan i databasen där du vill skapa extern tabell. Exempelvis kan du vill definiera en extern tabell för att få en aggregerad vy över katalogvyer eller DMV: er på dina utskalad data nivå. Eftersom katalogvyer och DMV: er redan finns lokalt och kan inte du använda namnen för den externa tabelldefinitionen. I stället använda ett annat namn och använda katalogvyn eller DMV i schemat\_namn och/eller OBJEKTET\_namn-satser. (Se exemplet nedan.) 

Instruktionen DISTRIBUTION anger Datadistribution som används för den här tabellen. Frågeprocessorn använder informationen i DISTRIBUTION-satsen för att bygga de effektivaste frågeplanerna.  

1. **SHARDADE** innebär att data partitioneras vågrätt mellan databaser. Partitionsnyckel för Datadistribution är den **< sharding_column_name >** parametern.
2. **REPLIKERADE** innebär att identiska kopior av tabellen finns på varje databas. Det är ditt ansvar att se till att replikerna är identiska mellan databaser.
3. **AVRUNDA\_ROBIN** innebär att tabellen är horisontellt med en metod för programvarudistribution för program-beroende. 

**Data på nivån referens**: den externa tabellen DDL refererar till en extern datakälla. Den externa datakällan anger en skärvkarta som ger den externa tabellen med informationen som behövs för att hitta alla databaser i din datanivå. 

### <a name="security-considerations"></a>Säkerhetsöverväganden
Användare med åtkomst till den externa tabellen tillgång automatiskt till de underliggande fjärrtabeller under autentiseringsuppgifterna som anges i definitionen av externa datakällan. Undvik oönskad rättighetsökning via autentiseringsuppgifterna för den externa datakällan. Använd GRANT eller REVOKE för en extern tabell precis som om det vore en vanlig tabell.  

När du har definierat den externa datakällan och dina externa tabeller, kan du nu använda fullständig T-SQL över dina externa tabeller.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exempel: fråga vågrätt partitionerade databaser
Följande fråga skapar en 3-vägs-koppling mellan informationslager, order och orderrader och använder flera samlingar och ett selektiv filter. Det förutsätter att (1) horisontell partitionering (sharding) och (2) att informationslager, order och orderrader är fragmenterade (sharded) som datalager id-kolumnen och att elastisk fråga kan samordna kopplingar till shards och bearbeta dyra delen av frågan på shards på parallellt. 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Lagrade proceduren för körning av T-SQL: sp\_execute_remote
Elastisk fråga introducerar också en lagrad procedur som ger direktåtkomst till shards. Den lagrade proceduren kallas [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) och kan användas för att köra remote lagrade procedurer eller T-SQL-kod på fjärr-databaser. Det tar följande parametrar: 

* Namn på datakälla (nvarchar): namnet på den externa datakällan av typen RDBMS. 
* Fråga (nvarchar): T-SQL-fråga som ska köras på varje shard. 
* Parameterdeklaration (nvarchar) - valfritt: sträng med datatypdefinitioner för de parametrar som används i Frågeparametern (till exempel sp_executesql). 
* Värdet parameterlistan - valfritt: kommaavgränsad lista över parametervärden (till exempel sp_executesql).

Sp\_köra\_remote använder den externa datakällan i startparametrar för att köra den angivna T-SQL-instruktionen på fjärr-databaser. Autentiseringsuppgifterna för den externa datakällan används för att ansluta till shardmap manager-databasen och remote databaserna.  

Exempel: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Anslutning för verktyg
Använda anslutningssträngar för vanliga SQL Server för att ansluta ditt program, ditt integreringsverktyg för BI och data till databasen med definitionerna extern tabell. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Sedan referens elastisk fråga i databasen som någon annan SQL Server-databas ansluten till verktyget och Använd externa tabeller från din verktyget eller ett program som om de vore lokala tabeller. 

## <a name="best-practices"></a>Bästa praxis
* Se till att elastisk fråga endpoint databasen har behörighet till shardmap-databasen och alla shards genom SQL DB-brandväggar.  
* Verifiera att rådet följs Datadistribution som definieras av den externa tabellen. Om din faktiska Datadistribution skiljer sig från distributionsplatser som angetts i din tabelldefinitionen, kan dina frågor ge oväntade resultat. 
* Elastisk fråga utför för närvarande inte fragment eliminering när predikat över nyckeln för horisontell partitionering skulle låta den för att undanta vissa shards på ett säkert sätt från bearbetning.
* Elastisk fråga fungerar bäst för frågor där de flesta av beräkningen kan göras på shards. Du kommer vanligtvis den bästa frågeprestanda med selektiv filter predikat som kan utvärderas på shards eller kopplingar partitionering nycklar som kan utföras på ett partitionsjusterade sätt på alla shards. Andra frågemönster kan behöva läsa in stora mängder data från shards till klustrets huvudnod och kan fungera dåligt

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk fråga översikt](sql-database-elastic-query-overview.md).
* Se en vertikal partitionering självstudie [komma igång med databasöverskridande frågor (vertikala partitioner)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs om horisontell partitionering (sharding) finns i [komma igång med elastisk fråga för horisontell partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktionen på en enskild remote Azure SQL-databas eller en uppsättning databaser som fungerar som shards i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
