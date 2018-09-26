---
title: Fråga över molndatabaser med olika scheman | Microsoft Docs
description: hur du ställer in frågor mellan databaser över vertikala partitioner
services: sql-database
ms.service: sql-database
subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3d94a52aaafca91d45d7f1a6406d3f998fbffe9f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161768"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Fråga mellan molndatabaser med olika scheman (förhandsversion)
![Fråga mellan tabeller i olika databaser][1]

Lodrätt partitionerade databaser använder olika uppsättningar med tabeller på olika databaser. Det innebär att schemat är annorlunda på olika databaser. Alla tabeller för inventering är exempel på en databas när alla redovisning-relaterade tabeller är på en andra databas. 

## <a name="prerequisites"></a>Förutsättningar
* Användaren måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten ingår behörigheten ALTER DATABASE.
* ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="overview"></a>Översikt

> [!NOTE]
> Till skillnad från med horisontell partitionering är dessa DDL-instruktionerna inte beroende definierar en datanivå med en skärvkarta via klientbiblioteket för elastiska databaser.
>

1. [SKAPA HUVUDNYCKEL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [SKAPA DATABASBEGRÄNSADE AUTENTISERINGSUPPGIFTER](https://msdn.microsoft.com/library/mt270260.aspx)
3. [SKAPA EXTERN DATAKÄLLA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [SKAPA EXTERN TABELL](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Skapa databasomfattande huvudnyckel och autentiseringsuppgifter
Autentiseringsuppgifterna används av elastisk fråga för att ansluta till din fjärranslutna databaser.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Se till att den `<username>` innehåller inte några **”\@servername”** suffix. 
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
> Typparametern måste anges till **RDBMS**. 
>

### <a name="example"></a>Exempel
I följande exempel visas hur CREATE-instruktionen för externa datakällor. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Att hämta listan över aktuella externa datakällor: 

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

I följande exempel visas hur du hämtar en lista över externa tabeller från databasen: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Kommentarer
Elastisk fråga utökar den befintliga externa table-syntaxen för att definiera externa tabeller som använder externa datakällor av typen RDBMS. En extern tabell-definition för vertikal partitionering innehåller följande aspekter: 

* **Schemat**: den externa tabellen DDL definierar ett schema som kan använda för dina frågor. Det tillhandahållna i din externa tabelldefinitionen schemat måste matcha schemat för tabellerna i fjärrdatabasen faktiska data ska lagras. 
* **Referens för fjärrdatabasen**: den externa tabellen DDL refererar till en extern datakälla. Den externa datakällan anger logiska servernamnet och databasnamnet för fjärrdatabasen där de faktiska data lagras. 

Med hjälp av en extern datakälla som beskrivs i föregående avsnitt, är syntaxen för att skapa externa tabeller följande: 

Instruktionen DATA_SOURCE definierar den externa datakällan (t.ex. fjärrdatabas vid vertikal partitionering) som används för den externa tabellen.  

SCHEMA_NAME och OBJECT_NAME satser ger möjlighet att mappa den externa tabelldefinitionen till en tabell i ett annat schema på fjärrdatabasen eller i en tabell med ett annat namn i respektive. Detta är användbart om du vill definiera en extern tabell till en katalogvy eller DMV på din fjärrdatabasen- eller andra fall där remote tabellens namn används redan lokalt.  

Följande DDL-instruktion utelämnar en befintliga externa tabelldefinitionen från den lokala katalogen. Det påverkar inte fjärrdatabasen. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Behörigheter för att skapa och släpp extern tabell**: ALTER ANY extern DATAKÄLLA behörigheter som krävs för extern tabell DDL som krävs också för att referera till den underliggande datakällan.  

## <a name="security-considerations"></a>Säkerhetsöverväganden
Användare med åtkomst till den externa tabellen tillgång automatiskt till de underliggande fjärrtabeller under autentiseringsuppgifterna som anges i definitionen av externa datakällan. Du bör noggrant hantera åtkomst till den externa tabellen för att undvika oönskad rättighetsökning via autentiseringsuppgifterna för den externa datakällan. Vanlig SQL-behörigheter kan användas för att BEVILJA eller ÅTERKALLA åtkomst till en extern tabell precis som om det vore en vanlig tabell.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exempel: fråga lodrätt partitionerade databaser
Följande fråga utför en 3-vägs-koppling mellan de lokala för order och orderrader och fjärrtabellen för kunder. Det här är ett exempel på användningsfall för referens data för elastisk fråga: 

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


## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Lagrade proceduren för körning av T-SQL: sp\_execute_remote
Elastisk fråga introducerar också en lagrad procedur som ger direktåtkomst till fjärrdatabasen. Den lagrade proceduren kallas [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) och kan användas för att köra remote lagrade procedurer eller T-SQL-kod på fjärrdatabasen. Det tar följande parametrar: 

* Namn på datakälla (nvarchar): namnet på den externa datakällan av typen RDBMS. 
* Fråga (nvarchar): T-SQL-fråga som ska köras på fjärrdatabasen. 
* Parameterdeklaration (nvarchar) - valfritt: sträng med datatypdefinitioner för de parametrar som används i Frågeparametern (till exempel sp_executesql). 
* Värdet parameterlistan - valfritt: kommaavgränsad lista över parametervärden (till exempel sp_executesql).

Sp\_köra\_remote använder den externa datakällan i startparametrar för att köra den angivna T-SQL-instruktionen på fjärrdatabasen. Autentiseringsuppgifterna för den externa datakällan används för att ansluta till fjärrdatabasen.  

Exempel: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 



## <a name="connectivity-for-tools"></a>Anslutning för verktyg
Du kan använda vanliga SQL Server-anslutningssträngar för att ansluta din integreringsverktyg BI och data till databaser med SQL DB-server som har elastisk fråga aktiverad och externa tabeller som har definierats. Kontrollera att SQL Server stöds som en datakälla för ditt verktyg. Referera till elastisk fråga i databasen och dess externa tabeller precis som andra SQL Server-databas som du ska ansluta till med verktyg. 

## <a name="best-practices"></a>Bästa praxis
* Se till att elastisk fråga endpoint databasen har behörighet till fjärrdatabasen genom att aktivera åtkomst för Azure-tjänster i dess SQL DB brandväggskonfigurationen. Se också till att autentiseringsuppgifterna som anges i definitionen av externa datakällan kan logga in fjärrdatabasen och har behörighet att komma åt fjärrtabellen.  
* Elastisk fråga fungerar bäst för frågor där de flesta av beräkningen kan göras på fjärr-databaser. Du kommer vanligtvis den bästa frågeprestanda med selektiv filter predikat som kan vara på fjärranslutna databaser eller kopplingar som kan utföras helt på fjärrdatabasen. Andra frågemönster kan behöva läsa in stora mängder data från fjärrdatabasen och kan prestera svagt. 

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk fråga översikt](sql-database-elastic-query-overview.md).
* Se en vertikal partitionering självstudie [komma igång med databasöverskridande frågor (vertikala partitioner)](sql-database-elastic-query-getting-started-vertical.md).
* En självstudiekurs om horisontell partitionering (sharding) finns i [komma igång med elastisk fråga för horisontell partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade data, se [fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktionen på en enskild remote Azure SQL-databas eller en uppsättning databaser som fungerar som shards i en vågrät partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
