---
title: Rapportering över utskalade molndatabaser
description: konfigurera elastiska frågor över vågräta partitioner
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823768"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportering över utskalade molndatabaser (förhandsgranskning)

![Fråga över shards][1]

Fragmenterade databaser distribuerar rader över en utskalad datanivå. Schemat är identiskt i alla deltagande databaser, även kallade horisontell partitionering. Med hjälp av en elastisk fråga kan du skapa rapporter som sträcker sig över alla databaser i en fragmenterad databas.

En snabb start finns i [Rapportering över utskalade molndatabaser](sql-database-elastic-query-getting-started.md).

För icke-fragmenterade databaser finns i [Fråga över molndatabaser med olika scheman](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Krav

* Skapa en fragmentkarta med hjälp av klientbiblioteket för elastisk databas. se [Hantering av fragmentkartläggning](sql-database-elastic-scale-shard-map-management.md). Eller använd exempelappen i [Komma igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md).
* Du kan också se [Migrera befintliga databaser till utskalade databaser](sql-database-elastic-convert-to-use-elastic-tools.md).
* Användaren måste ha behörigheten ÄNDRA extern datakälla. Den här behörigheten ingår i behörigheten ALTER DATABASE.
* ÄNDRA EVENTUELLA externa datakällbehörigheter behövs för att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt

Dessa satser skapar metadatarepresentationen av den fragmenterade datanivån i den elastiska frågedatabasen.

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASSCOPED-AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Skapa huvudnyckel och autentiseringsuppgifter för databasscope

Autentiseringsuppgifterna används av den elastiska frågan för att ansluta till fjärrdatabaserna.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Kontrollera att suffixet " användarnamn " inte innehåller något " servernamn.Se till att suffixet *"\<användarnamn\>"* inte innehåller något " *\@servernamn.*

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

Den externa datakällan refererar till fragmentkartan. En elastisk fråga använder sedan den externa datakällan och den underliggande fragmentmappningen för att räkna upp de databaser som deltar i datanivån.
Samma autentiseringsuppgifter används för att läsa fragmentmappningen och för att komma åt data på shards under bearbetningen av en elastisk fråga.

## <a name="13-create-external-tables"></a>1.3 Skapa externa tabeller

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

Hämta listan över externa tabeller från den aktuella databasen:

    SELECT * from sys.external_tables;

Så här släpper du externa tabeller:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Anmärkningar

Satsen\_DATAKÄLLA definierar den externa datakällan (en fragmentkarta) som används för den externa tabellen.  

SCHEMANAMN\_och\_OBJEKTNAMN mappar den externa tabelldefinitionen till en tabell i ett annat schema. Om det utelämnas antas fjärrobjektets schema vara "dbo" och dess namn antas vara identiskt med det externa tabellnamnet som definieras. Detta är användbart om namnet på fjärrtabellen redan finns i databasen där du vill skapa den externa tabellen. Du vill till exempel definiera en extern tabell för att få en samlad vy över katalogvyer eller DMV:er på den utskalade datanivån. Eftersom katalogvyer och DMVs redan finns lokalt kan du inte använda deras namn för den externa tabelldefinitionen. Använd i stället ett annat namn och använd katalogvyns eller DMV:s namn i schemanamns-\_och/eller objektnamnssatserna.\_ (Se exemplet nedan.)

DISTRIBUTION-satsen anger den datadistribution som används för den här tabellen. Frågeprocessorn använder informationen i DISTRIBUTION-satsen för att skapa de mest effektiva frågeplanerna.

1. **SHARDED** innebär att data är horisontellt partitionerade över databaserna. Partitioneringsnyckeln för datadistributionen är **parametern<sharding_column_name>.**
2. **REPLIKERAD** innebär att det finns identiska kopior av tabellen i varje databas. Det är ditt ansvar att se till att replikerna är identiska i databaserna.
3. **ROUND\_ROBIN** innebär att tabellen är vågrätt partitionerad med hjälp av en programberoende distributionsmetod.

**Referens på datanivå**: Den externa tabellen DDL refererar till en extern datakälla. Den externa datakällan anger en fragmentkarta som ger den externa tabellen den information som behövs för att hitta alla databaser på datanivån.

### <a name="security-considerations"></a>Säkerhetsöverväganden

Användare med åtkomst till den externa tabellen får automatiskt åtkomst till de underliggande fjärrtabellerna under den autentiseringsuppgifter som anges i definitionen av extern datakälla. Undvik oönskad behörighetshöjning via referensen för den externa datakällan. Använd GRANT eller REVOKE för en extern tabell precis som om det vore en vanlig tabell.  

När du har definierat din externa datakälla och dina externa tabeller kan du nu använda fullständig T-SQL över dina externa tabeller.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exempel: fråga efter vågräta partitionerade databaser

Följande fråga utför en trevägskoppling mellan lagerställen, order och orderrader och använder flera aggregat och ett selektivt filter. Det förutsätts (1) horisontell partitionering (fragmentering) och (2) att lagerställen, order och orderrader fragmenteras av lager-ID-kolumnen och att den elastiska frågan kan samlokalisera kopplingarna på fragmenten och bearbeta den dyra delen av frågan på fragmenten i Parallell.

```sql
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
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Lagrad procedur för fjärr-T-SQL-körning: sp\_execute_remote

Elastisk fråga introducerar också en lagrad procedur som ger direkt åtkomst till shards. Den lagrade proceduren kallas [sp\_kör \_fjärrkontroll](https://msdn.microsoft.com/library/mt703714) och kan användas för att köra fjärrbevarade procedurer eller T-SQL-kod på fjärrdatabaserna. Det tar följande parametrar:

* Datakällnamn (nvarchar): Namnet på den externa datakällan för typen RDBMS.
* Fråga (nvarchar): Den T-SQL-fråga som ska köras på varje shard.
* Parameterdeklaration (nvarchar) - valfritt: Sträng med datatypsdefinitioner för de parametrar som används i parametern Query (t.ex. sp_executesql).
* Parametervärdelista - valfri: Kommaavgränsad lista över parametervärden (t.ex. sp_executesql).

F-körningsfjärrklassen\_\_använder den externa datakällan som anges i anropsparametrarna för att köra det angivna T-SQL-uttrycket på fjärrdatabaserna. Den använder autentiseringsuppgifterna för den externa datakällan för att ansluta till shardmap manager-databasen och fjärrdatabaserna.  

Exempel:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Använd vanliga SQL Server-anslutningssträngar för att ansluta ditt program, din BI och dataintegrationsverktyg till databasen med dina externa tabelldefinitioner. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Referera sedan till den elastiska frågedatabasen som alla andra SQL Server-databaser som är anslutna till verktyget och använd externa tabeller från verktyget eller programmet som om de vore lokala tabeller.

## <a name="best-practices"></a>Bästa praxis

* Kontrollera att den elastiska frågeslutpunktsdatabasen har fått åtkomst till fragmentmappsdatabasen och alla shards via SQL DB-brandväggarna.  
* Validera eller framtvinga den datadistribution som definieras av den externa tabellen. Om din faktiska datadistribution skiljer sig från den distribution som anges i tabelldefinitionen kan dina frågor ge oväntade resultat.
* Elastisk fråga utför för närvarande inte fragmenteliminering när predikat över sharding-nyckeln gör det möjligt att säkert utesluta vissa shards från bearbetning.
* Elastisk fråga fungerar bäst för frågor där det mesta av beräkningen kan göras på shards. Du får vanligtvis den bästa frågeprestanda med selektiva filter predikater som kan utvärderas på shards eller kopplingar över partitioneringsnycklar som kan utföras på ett partitionsjusterat sätt på alla shards. Andra frågemönster kan behöva läsa in stora mängder data från shards till huvudnoden och kan fungera dåligt

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md).
* En lodrät partitioneringsdjälva finns i [Komma igång med fråga över flera databaser (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Syntax- och exempelfrågor för lodrätt partitionerade data finns i [Fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs för vågrät partitionering (sharding) finns i [Komma igång med elastisk fråga för vågrät partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Se [\_sp \_kör fjärrkontroll](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-uttryck på en enda fjärr Azure SQL-databas eller uppsättning databaser som fungerar som shards i ett vågrätt partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
