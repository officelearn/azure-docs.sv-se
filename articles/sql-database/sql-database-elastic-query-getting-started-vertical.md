---
title: Kom igång med databasöverskridande frågor (vertikala partitioner) | Microsoft Docs
description: hur du använder elastic database-fråga med lodrätt partitionerade databaser
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sstein
ms.openlocfilehash: 62dda46f2f78b01722016a9bbd1e6db05814a0bf
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448568"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Kom igång med databasöverskridande frågor (vertikala partitioner) (förhandsversion)

Elastisk databasfråga (förhandsversion) för Azure SQL Database kan du köra T-SQL-frågor som sträcker sig över flera databaser med hjälp av en enda anslutningspunkt. Den här artikeln gäller [vertikalt databaser](sql-database-elastic-query-vertical-partitioning.md).  

När du är klar kommer du att: Lär dig hur du konfigurerar och använder en Azure SQL Database för att köra frågor som sträcker sig över flera relaterade databaser.

Läs mer om elastic database-fråga funktionen [översikt över Azure SQL Database elastic database-fråga](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Behörigheten ALTER ANY extern DATAKÄLLA krävs. Den här behörigheten ingår behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="create-the-sample-databases"></a>Skapa exemplet-databaser

Börja med att skapa två databaserna, **kunder** och **order**, antingen i samma eller olika logiska servrar.

Kör följande frågor på den **order** databasen för att skapa den **OrderInformation** tabellen och ange exempeldata.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Nu kan köra följande fråga på den **kunder** databasen för att skapa den **CustomerInformation** tabellen och ange exempeldata.

    CREATE TABLE [dbo].[CustomerInformation](
        [CustomerID] [int] NOT NULL,
        [CustomerName] [varchar](50) NULL,
        [Company] [varchar](50) NULL
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
    )
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')

## <a name="create-database-objects"></a>Skapa databasobjekt

### <a name="database-scoped-master-key-and-credentials"></a>Databasbegränsade autentiseringsuppgifter och huvudnyckeln

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till orderdatabasen och kör följande T-SQL-kommandon:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    ”Användarnamn” och ”password” ska vara det användarnamn och lösenord som används för att logga in till kunder-databasen.
    Autentisering med hjälp av Azure Active Directory med elastiska frågor stöds inte för närvarande.

### <a name="external-data-sources"></a>Externa datakällor

Skapa en extern datakälla genom att köra följande kommando på orderdatabasen:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Externa tabeller

Skapa en extern tabell på orderdatabasen som matchar definitionen av tabellen CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Kör en exempelfråga elastisk databas T-SQL

När du har definierat den externa datakällan och dina externa tabeller kan använda du nu T-SQL för att fråga efter dina externa tabeller. Kör frågan på orderdatabasen:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Kostnad

För närvarande ingår elastic database-fråga funktionen i kostnaden för din Azure SQL Database.  

Information om priser finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk fråga översikt](sql-database-elastic-query-overview.md).
* Syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs om horisontell partitionering (sharding) finns i [komma igång med elastisk fråga för horisontell partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade data, se [fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktionen på en enskild remote Azure SQL-databas eller en uppsättning databaser som fungerar som shards i en vågrät partitioneringsschema.
