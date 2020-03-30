---
title: Fråga över molndatabaser med olika schema
description: konfigurera frågor över flera databaser över lodräta partitioner
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823721"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Fråga över molndatabaser med olika scheman (förhandsgranskning)

![Fråga över tabeller i olika databaser][1]

Vertikalt partitionerade databaser använder olika uppsättningar tabeller i olika databaser. Det innebär att schemat är olika på olika databaser. Alla tabeller för lager finns till exempel i en databas medan alla redovisningsrelaterade tabeller finns i en andra databas. 

## <a name="prerequisites"></a>Krav

* Användaren måste ha behörigheten ÄNDRA extern datakälla. Den här behörigheten ingår i behörigheten ALTER DATABASE.
* ÄNDRA EVENTUELLA externa datakällbehörigheter behövs för att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt

> [!NOTE]
> Till skillnad från horisontell partitionering är dessa DDL-satser inte beroende av att definiera en datanivå med en fragmentkarta via klientbiblioteket för elastisk databas.
>

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASSCOPED-AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Skapa huvudnyckel och autentiseringsuppgifter för databasomfattade

Autentiseringsuppgifterna används av den elastiska frågan för att ansluta till fjärrdatabaserna.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Kontrollera att `<username>` suffixet "servernamn" inte innehåller något **"servernamn".\@** 
>

## <a name="create-external-data-sources"></a>Skapa externa datakällor

Syntax:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> Parametern TYPE måste anges till **RDBMS**. 
>

### <a name="example"></a>Exempel

Följande exempel illustrerar användningen av CREATE-satsen för externa datakällor. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Så här hämtar du listan över aktuella externa datakällor: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Externa tabeller

Syntax:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exempel

```sql
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 
```

I följande exempel visas hur du hämtar listan över externa tabeller från den aktuella databasen: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Anmärkningar

Elastisk fråga utökar den befintliga externa tabellsyntaxen för att definiera externa tabeller som använder externa datakällor av typen RDBMS. En extern tabelldefinition för vertikal partitionering omfattar följande aspekter: 

* **Schema**: Den externa tabellen DDL definierar ett schema som dina frågor kan använda. Schemat som anges i den externa tabelldefinitionen måste matcha schemat för tabellerna i fjärrdatabasen där de faktiska data lagras. 
* **Referens för fjärrdatabas:** DDL för extern tabell refererar till en extern datakälla. Den externa datakällan anger SQL Database-servernamnet och databasnamnet för fjärrdatabasen där de faktiska tabelldata lagras. 

Med hjälp av en extern datakälla som beskrivs i föregående avsnitt är syntaxen för att skapa externa tabeller följande: 

Den DATA_SOURCE satsen definierar den externa datakällan (dvs. fjärrdatabasen vid vertikal partitionering) som används för den externa tabellen.  

Satserna SCHEMA_NAME och OBJECT_NAME ger möjlighet att mappa den externa tabelldefinitionen till en tabell i ett annat schema i fjärrdatabasen eller till en tabell med ett annat namn. Detta är användbart om du vill definiera en extern tabell till en katalogvy eller DMV på fjärrdatabasen - eller någon annan situation där fjärrtabellnamnet redan tas lokalt.  

Följande DDL-sats släpper en befintlig extern tabelldefinition från den lokala katalogen. Det påverkar inte fjärrdatabasen. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Behörigheter för EXTERN TABELL SKAPA/SLÄPP**: ÄNDRA EVENTUELLA externa datakällbehörigheter behövs för extern tabell-DDL som också behövs för att referera till den underliggande datakällan.  

## <a name="security-considerations"></a>Säkerhetsöverväganden

Användare med åtkomst till den externa tabellen får automatiskt åtkomst till de underliggande fjärrtabellerna under den autentiseringsuppgifter som anges i definitionen av extern datakälla. Du bör noggrant hantera åtkomsten till den externa tabellen för att undvika oönskad behörighetshöjning via autentiseringsuppgifterna för den externa datakällan. Vanliga SQL-behörigheter kan användas för att bevilja eller återkalla åtkomst till en extern tabell precis som om det vore en vanlig tabell.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exempel: fråga vertikalt partitionerade databaser

Följande fråga utför en trevägskoppling mellan de två lokala tabellerna för order och orderrader och fjärrtabellen för kunder. Detta är ett exempel på referensdata användningsfall för elastisk fråga: 

```sql
    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Lagrad procedur för fjärr-T-SQL-körning: sp\_execute_remote

Elastisk fråga introducerar också en lagrad procedur som ger direkt åtkomst till fjärrdatabasen. Den lagrade proceduren kallas [sp\_kör \_fjärrkontroll](https://msdn.microsoft.com/library/mt703714) och kan användas för att köra fjärrbevarade procedurer eller T-SQL-kod på fjärrdatabasen. Det tar följande parametrar: 

* Datakällnamn (nvarchar): Namnet på den externa datakällan för typen RDBMS. 
* Fråga (nvarchar): Den T-SQL-fråga som ska köras i fjärrdatabasen. 
* Parameterdeklaration (nvarchar) - valfritt: Sträng med datatypsdefinitioner för de parametrar som används i parametern Query (t.ex. sp_executesql). 
* Parametervärdelista - valfri: Kommaavgränsad lista över parametervärden (t.ex. sp_executesql).

F-körningsfjärrklassen\_\_använder den externa datakällan som anges i anropsparametrarna för att köra det angivna T-SQL-uttrycket på fjärrdatabasen. Den använder autentiseringsuppgifterna för den externa datakällan för att ansluta till fjärrdatabasen.  

Exempel: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server-anslutningssträngar för att ansluta bi- och dataintegrationsverktygen till databaser på SQL DB-servern som har elastiska frågeaktiverade och externa tabeller definierade. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Se sedan den elastiska frågedatabasen och dess externa tabeller precis som alla andra SQL Server-databaser som du skulle ansluta till med verktyget. 

## <a name="best-practices"></a>Bästa praxis

* Kontrollera att den elastiska frågeslutpunktsdatabasen har fått åtkomst till fjärrdatabasen genom att aktivera åtkomst för Azure Services i sql DB-brandväggskonfigurationen. Kontrollera också att autentiseringsuppgifterna i definitionen av extern datakälla kan logga in på fjärrdatabasen och har behörighet att komma åt fjärrtabellen.  
* Elastisk fråga fungerar bäst för frågor där det mesta av beräkningen kan göras på fjärrdatabaserna. Du får vanligtvis bästa frågeprestanda med selektiva filter predikater som kan utvärderas på fjärrdatabaser eller kopplingar som kan utföras helt på fjärrdatabasen. Andra frågemönster kan behöva läsa in stora mängder data från fjärrdatabasen och kan fungera dåligt. 

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md).
* En lodrät partitioneringsdjälva finns i [Komma igång med fråga över flera databaser (lodrät partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* En självstudiekurs för vågrät partitionering (sharding) finns i [Komma igång med elastisk fråga för vågrät partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax- och exempelfrågor för vågrätt partitionerade data finns i [Fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [\_sp \_kör fjärrkontroll](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-uttryck på en enda fjärr Azure SQL-databas eller uppsättning databaser som fungerar som shards i ett vågrätt partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
