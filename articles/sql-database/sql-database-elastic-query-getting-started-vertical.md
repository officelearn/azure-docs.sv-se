---
title: Kom igång med flera databaser frågor (vertikal partitionering) | Microsoft Docs
description: hur du använder elastisk databasfrågan med lodrätt partitionerade databaser
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c7bf6816b457f7e193f53336c48f5e205722067e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Kom igång med flera databaser frågor (vertikal partitionering) (förhandsgranskning)
Elastisk databasfrågan (förhandsversion) för Azure SQL Database låter dig köra T-SQL-frågor som sträcker sig över flera databaser med hjälp av en enda anslutningspunkt. Det här avsnittet gäller [lodrätt partitionerad databaser](sql-database-elastic-query-vertical-partitioning.md).  

När du är klar, kommer: Lär dig att konfigurera och köra frågor som sträcker sig över flera relaterade databaser med en Azure SQL Database. 

Mer information om funktionen för elastisk databas frågan finns [översikt över Azure SQL Database elastisk databas frågan](sql-database-elastic-query-overview.md). 

## <a name="prerequisites"></a>Förutsättningar

Du måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten har behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till den underliggande datakällan.

## <a name="create-the-sample-databases"></a>Skapa exempeldatabasen
Börja med, måste vi skapa två databaserna, **kunder** och **order**, antingen i samma eller andra logiska servrar.   

Kör följande frågor på den **order** databas för att skapa den **OrderInformation** tabell och ange exempeldata. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Nu kan köra följande fråga på den **kunder** databas för att skapa den **CustomerInformation** tabell och ange exempeldata. 

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
### <a name="database-scoped-master-key-and-credentials"></a>Databasbegränsade huvudnyckel och autentiseringsuppgifter
1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Ansluta till order-databasen och kör följande T-SQL-kommandon:
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    ”Användarnamn” och ”password” ska vara användarnamn och lösenord som används för att logga in i databasen kunder.
    Autentisering med hjälp av Azure Active Directory med elastisk frågor stöds inte för närvarande.

### <a name="external-data-sources"></a>Externa datakällor
Om du vill skapa en extern datakälla, kör du följande kommando i order-databasen: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Externa tabeller
Skapa en extern tabell på order-databasen, som matchar definitionen av tabellen CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra en exempelfråga för elastisk databas T-SQL
När du har definierat den externa datakällan och externa tabeller kan du nu använda T-SQL fråga din externa tabeller. Kör frågan på order-databasen: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Kostnad
Funktionen elastisk databas frågan är för närvarande inkluderas i kostnaden för Azure SQL Database.  

Mer information om priser finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database). 

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga, se [elastisk frågan översikt](sql-database-elastic-query-overview.md).
* Syntax och exempel frågor för lodrätt partitionerade finns [frågar lodrätt partitionerad data)](sql-database-elastic-query-vertical-partitioning.md)
* Horisontell partitionering (delning), finns [komma igång med elastisk frågan för horisontell partitionering (delning)](sql-database-elastic-query-getting-started.md).
* Syntax och exempel frågor för vågrätt partitionerade finns [frågar vågrätt partitionerad data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [sp\_köra \_remote](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-instruktion på en enda remote Azure SQL Database eller en uppsättning databaser som fungerar som delar i en vågrät partitioneringsschema.
