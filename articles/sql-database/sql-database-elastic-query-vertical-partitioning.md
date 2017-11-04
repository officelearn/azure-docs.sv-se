---
title: "Frågan över moln databaser med olika schema | Microsoft Docs"
description: "hur du ställer in mellan databasfrågor över lodräta partitioner"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: 84c261f2-9edc-42f4-988c-cf2f251f5eff
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: torsteng
ms.openlocfilehash: d57f45066387f451463a38d76d3fe6adab77e41f
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Fråga på molnet databaser med olika scheman (förhandsgranskning)
![Fråga på tabeller i olika databaser][1]

Lodrätt partitionerad-databaser använder olika uppsättningar med tabeller på olika databaser. Det innebär att schemat är olika för olika databaser. Till exempel finns alla tabeller för lager på en databas när alla redovisning-relaterade tabeller är på en andra databas. 

## <a name="prerequisites"></a>Krav
* Användaren måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten har behörigheten ALTER DATABASE.
* ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt

> [!NOTE]
> Till skillnad från med horisontell partitionering är dessa DDL-satser inte beroende definierar en datanivå med en Fragmentera karta via klientbibliotek för elastisk databas.
>

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASBEGRÄNSADE AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Skapa huvudnyckel för databasen omfång och autentiseringsuppgifter
Autentiseringsuppgifterna används av elastisk frågan för att ansluta till din fjärranslutna databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Se till att den `<username>` innehåller inte några **”@servername”** suffix. 
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
I följande exempel illustrerar hur instruktionen CREATE för externa datakällor. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Att hämta lista över aktuella externa datakällor: 

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

I följande exempel visas hur du hämtar listan över externa tabeller från databasen: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Kommentarer
Elastisk frågan utökar den befintliga externa table-syntaxen för att definiera externa tabeller som använder externa datakällor av typen RDBMS. En extern tabelldefinition för vertikal partitionering omfattar följande: 

* **Schemat**: den externa tabellen DDL definierar ett schema som kan använda för dina frågor. Angivet schema i den externa tabelldefinitionen måste matcha schemat för tabellerna i fjärrdatabasen där faktiska data lagras. 
* **Fjärrdatabasen referens**: den externa tabellen DDL refererar till en extern datakälla. Den externa datakällan anger logiska servernamnet och databasnamnet på fjärrdatabasen där den faktiska informationen lagras. 

Med hjälp av en extern datakälla som beskrivs i föregående avsnitt, är syntax för att skapa externa tabeller följande: 

Instruktionen DATA_SOURCE definierar den externa datakällan (d.v.s. fjärrdatabasen vid vertikal partitionering) som används för den externa tabellen.  

SCHEMA_NAME och OBJECT_NAME satser ger möjlighet att mappa den externa tabelldefinitionen till en tabell i ett annat schema på fjärrdatabasen eller till en tabell med ett annat namn i respektive. Detta är användbart om du vill definiera en extern tabell till en katalog eller DMV i fjärrdatabasen- eller någon annan situation där remote tabellens namn är upptaget lokalt.  

Följande DDL-instruktionen utelämnar en externa tabelldefinitionen från den lokala katalogen. Det påverkar inte fjärrdatabasen. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Behörighet för CREATE/DROP extern tabell**: ALTER ANY extern DATAKÄLLA behörigheter krävs för extern tabell DDL som krävs också för att referera till den underliggande datakällan.  

## <a name="security-considerations"></a>Säkerhetsöverväganden
Användare med åtkomst till extern tabell få automatiskt åtkomst till de underliggande fjärrtabeller under autentiseringen i definitionen av externa datakällan. Du bör noggrant hantera åtkomst till extern tabell för att undvika oönskad höjning av privilegier genom autentiseringsuppgifterna för den externa datakällan. Vanlig SQL behörigheter kan användas för att BEVILJA eller ÅTERKALLA åtkomst till en extern tabell precis som om det vore en vanlig tabell.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exempel: frågar lodrätt partitionerad databaser
Följande fråga utför en trevägs koppling mellan de lokala för rader och order och fjärrtabellen för kunder. Detta är ett exempel på användningsfall för referens data för elastiska frågan: 

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
Du kan använda vanliga anslutningssträngar för SQL Server för att ansluta din verktyg för BI och integrering till databaser med SQL DB-server som har elastisk frågan aktiverad och externa tabeller har definierats. Kontrollera att SQL Server stöds som en datakälla för verktyget du behöver. Referera till elastisk fråga databas och dess externa tabeller precis som andra SQL Server-databas som du vill ansluta till med din-verktyget. 

## <a name="best-practices"></a>Bästa praxis
* Se till att elastisk fråga endpoint databas har fått tillgång till fjärrdatabasen genom att aktivera åtkomst för Azure-tjänster i dess brandväggskonfigurationen för SQL-databas. Kontrollera också att autentiseringsuppgifterna som anges i definitionen av externa datakällan kan logga in fjärrdatabasen och har behörighet att komma åt fjärrtabellen.  
* Elastisk frågan fungerar bäst för frågor där de flesta av beräkningen kan göras på fjärr-databaser. Du får vanligtvis den bästa möjliga prestandan med selektiv filter-predikat som kan utvärderas på fjärranslutna databaser eller kopplingar som kan utföras helt i fjärrdatabasen. Andra frågemönster kan behöva läsa in stora mängder data från fjärrdatabasen och kan fungera dåligt. 

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk frågan översikt](sql-database-elastic-query-overview.md).
* Vertikal partitionering, finns [komma igång med flera databaser fråga (vertikal partitionering)](sql-database-elastic-query-getting-started-vertical.md).
* Horisontell partitionering (delning), finns [komma igång med elastisk frågan för horisontell partitionering (delning)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade finns [frågar vågrätt partitionerad data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktion på en enda remote Azure SQL Database eller en uppsättning databaser som fungerar som delar i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
