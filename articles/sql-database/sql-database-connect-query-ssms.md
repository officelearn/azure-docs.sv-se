<properties
    pageTitle="Anslut till SQL Database, SQL Server Management Studio | Microsoft Azure"
    description="Lär dig hur du ansluter till SQL Database på Azure med hjälp av SQL Server Management Studio (SSMS). Kör sedan en exempelfråga med Transact-SQL (T-SQL)."
    metaCanonical=""
    keywords="connect to sql database,sql server management studio"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="" />

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sstein;carlrab" />

# Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Den här artikeln visar hur du ansluter till en Azure SQL-databas med den senaste versionen av SQL Server Management Studio (SSMS) och genomför en enkel fråga med Transact-SQL-uttryck (T-SQL).

[AZURE.INCLUDE [Sign in](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [SSMS Connect](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Information om brandväggsregler finns i [Konfigurera brandväggsinställningar (Azure SQL Database)](sql-database-configure-firewall-settings.md).

## Kör exempelfrågor

Efter att du ansluter till din logiska server, kan du ansluta till en databas och köra en exempelfråga. 

1. I **Object Explorer**, navigerar du till en databas på den server där du har behörighet, som exempeldatabasen **AdventureWorks**.
2. Högerklicka på databasen och välj **Ny fråga**.

    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Kopiera och klistra in följande kod i frågefönstret.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Klicka på **Kör**-knappen.  Följande skärmbild visar en lyckad fråga.

    ![Lyckades. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Nästa steg

Du kan använda T-SQL-uttryck för att skapa och hantera databaser i Azure på samma sätt som med SQL Server. Om du är bekant med T-SQL med SQL Server, kan du se [Azure SQL Database Transact-SQL-information)](sql-database-transact-sql-information.md) för en sammanställning av skillnaderna.

Om T-SQL är nytt för dig, se [Självstudier: Skriva Transact-SQL-uttryck](https://msdn.microsoft.com/library/ms365303.aspx) och [Transact-SQL-referens (databasmotor)](https://msdn.microsoft.com/library/bb510741.aspx).

För att komma igång med att skapa databasanvändare och administratörer för databasanvändare, kan du se [Kom igång med Azure SQL Database-säkerhet](sql-database-get-started-security.md)



<!--HONumber=Jun16_HO2-->


