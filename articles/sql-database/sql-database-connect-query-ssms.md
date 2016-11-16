---
title: Anslut till SQL Database, SQL Server Management Studio | Microsoft Docs
description: "Lär dig hur du ansluter till SQL Database på Azure med hjälp av SQL Server Management Studio (SSMS). Kör sedan en exempelfråga med Transact-SQL (T-SQL)."
metacanonical: 
keywords: anslut till sql database, sql server management studio
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0eb25eb76c6c6c2446ac0b2b07c65975c3719db0


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-tsql-query"></a>Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 
> 

I den här artikeln får du lära dig hur du ansluter till en Azure SQL-databas med hjälp av SQL Server Management Studio (SSMS). När du har anslutit kör vi en enkel Transact-SQL-fråga (T-SQL) för att kontrollera kommunikationen med databasen.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[!INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

## <a name="run-sample-queries"></a>Kör exempelfrågor
När du har anslutit till din logiska server kan du ansluta till en databas och köra en exempelfråga. Om du är nybörjare på att skriva frågor kan du läsa [Skriva Transact-SQL-uttryck](https://msdn.microsoft.com/library/ms365303.aspx).

1. I **Object Explorer** navigerar du till en databas på servern, som exempeldatabasen **AdventureWorks**.
2. Högerklicka på databasen och välj sedan **Ny fråga**:
   
    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)
3. Kopiera och klistra in följande i frågefönstret:
   
        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;
4. Klicka på **Kör**-knappen:
   
    ![Lyckades. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## <a name="next-steps"></a>Nästa steg
Du kan använda T-SQL-uttryck för att skapa och hantera databaser i Azure på samma sätt som med SQL Server. Om du är bekant med T-SQL med SQL Server, kan du se [Azure SQL Database Transact-SQL-information)](sql-database-transact-sql-information.md) för en sammanställning av skillnaderna.

Om T-SQL är nytt för dig, se [Självstudier: Skriva Transact-SQL-uttryck](https://msdn.microsoft.com/library/ms365303.aspx) och [Transact-SQL-referens (databasmotor)](https://msdn.microsoft.com/library/bb510741.aspx).

För att komma igång med att skapa databasanvändare och administratörer för databasanvändare, kan du se [Kom igång med Azure SQL Database-säkerhet](sql-database-get-started-security.md)

Mer information om SSMS finns i [Använda SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).




<!--HONumber=Nov16_HO2-->


