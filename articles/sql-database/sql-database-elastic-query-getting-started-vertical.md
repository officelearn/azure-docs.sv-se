---
title: Komma igång med frågor över flera databaser
description: använda elastisk databasfråga med vertikalt partitionerade databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: af93035766eaf1afa12d124b8379ee55c5567260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823803"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Komma igång med frågor över flera databaser (lodrät partitionering) (förhandsgranskning)

Med elastisk databasfråga (förhandsversion) för Azure SQL Database kan du köra T-SQL-frågor som sträcker sig över flera databaser med en enda anslutningspunkt. Den här artikeln gäller [vertikalt partitionerade databaser](sql-database-elastic-query-vertical-partitioning.md).  

När du är klar får du lära dig hur du konfigurerar och använder en Azure SQL-databas för att utföra frågor som sträcker sig över flera relaterade databaser.

Mer information om funktionen för elastisk databasfråga finns i [Azure SQL Databases överblick över elastisk databasfråga](sql-database-elastic-query-overview.md).

## <a name="prerequisites"></a>Krav

Ändra eventuell behörighet för extern datakälla krävs. Den här behörigheten ingår i behörigheten ALTER DATABASE. ÄNDRA EVENTUELLA externa datakällbehörigheter behövs för att referera till den underliggande datakällan.

## <a name="create-the-sample-databases"></a>Skapa exempeldatabaserna

Till att börja med skapar du två databaser, **Kunder** och **order**, antingen på samma eller olika SQL Database-servrar.

Kör följande frågor i **orderdatabasen** för att skapa tabellen **OrderInformation** och mata in exempeldata.

    CREATE TABLE [dbo].[OrderInformation](
        [OrderID] [int] NOT NULL,
        [CustomerID] [int] NOT NULL
        )
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)

Kör nu följande fråga i **kunddatabasen** för att skapa tabellen **CustomerInformation** och mata in exempeldata.

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

### <a name="database-scoped-master-key-and-credentials"></a>Huvudnyckel och autentiseringsuppgifter för databasomfattade

1. Öppna SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Anslut till orderdatabasen och kör följande T-SQL-kommandon:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';  

    "Användarnamn" och "lösenord" ska vara användarnamn och lösenord som används för att logga in i kunddatabasen.
    Autentisering med Azure Active Directory med elastiska frågor stöds för närvarande inte.

### <a name="external-data-sources"></a>Externa datakällor

Om du vill skapa en extern datakälla kör du följande kommando i orderdatabasen:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
        (TYPE = RDBMS,
        LOCATION = '<server_name>.database.windows.net',
        DATABASE_NAME = 'Customers',
        CREDENTIAL = ElasticDBQueryCred,
    ) ;

### <a name="external-tables"></a>Externa tabeller

Skapa en extern tabell i databasen Order, som matchar definitionen av tabellen CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
    ( [CustomerID] [int] NOT NULL,
      [CustomerName] [varchar](50) NOT NULL,
      [Company] [varchar](50) NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc)

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Köra en exempel elastisk databas T-SQL-fråga

När du har definierat din externa datakälla och dina externa tabeller kan du nu använda T-SQL för att fråga dina externa tabeller. Kör den här frågan i orderdatabasen:

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
    FROM OrderInformation
    INNER JOIN CustomerInformation
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID

## <a name="cost"></a>Kostnad

För närvarande ingår den elastiska databasfråganfunktionen i kostnaden för din Azure SQL-databas.  

Prisinformation finns i [SQL Database Pricing](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md).
* Syntax- och exempelfrågor för lodrätt partitionerade data finns i [Fråga lodrätt partitionerade data)](sql-database-elastic-query-vertical-partitioning.md)
* En självstudiekurs för vågrät partitionering (sharding) finns i [Komma igång med elastisk fråga för vågrät partitionering (sharding)](sql-database-elastic-query-getting-started.md).
* Syntax- och exempelfrågor för vågrätt partitionerade data finns i [Fråga vågrätt partitionerade data)](sql-database-elastic-query-horizontal-partitioning.md)
* Se [\_sp \_kör fjärrkontroll](https://msdn.microsoft.com/library/mt703714) för en lagrad procedur som kör en Transact-SQL-uttryck på en enda fjärr Azure SQL-databas eller uppsättning databaser som fungerar som shards i ett vågrätt partitioneringsschema.
