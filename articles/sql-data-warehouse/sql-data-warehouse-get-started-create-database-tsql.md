<properties
   pageTitle="Skapa ett SQL Data Warehouse med TSQL | Microsoft Azure"
   description="Lär dig hur du skapar ett Azure SQL Data Warehouse med TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Skapa en SQL Data Warehouse-databas med hjälp av Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

I den här artikeln visas hur du skapar ett SQL Data Warehouse med T-SQL.

## Krav

Du behöver följande för att komma igång: 

- **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][] eller [MSDN Azure-krediter][] för att skapa ett konto.
- **Azure SQL Server**: Se [Skapa en logisk Azure SQL Database-server med Azure Portal][] eller [Skapa en logisk Azure SQL Database-server med PowerShell][] för mer information.
- **Resursgrupp**: Använd antingen samma resursgrupp som din Azure SQL-server eller se [hur du kan skapa en resursgrupp][].
- **Miljö för att köra T-SQL**: Du kan använda [Visual Studio][Installera Visual Studio och SSDT], [sqlcmd][], eller [SSMS][] för att köra T-SQL.

> [AZURE.NOTE] Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Se [Priser för SQL Data Warehouse][] för mer information om priser.

## Skapa en databas med Visual Studio

Om du precis kommit igång med Visual Studio, kan du se artikeln [Fråga SQL Data Warehouse (Visual Studio)][].  För att starta, öppnar du SQL Server Object Explorer i Visual Studio och ansluter till servern där din SQL Data Warehouse-databas kommer ligga.  När du väl är ansluten, kan du skapa ett SQL Data Warehouse genom att köra följande SQL-kommando mot **huvud**-databasen.  Det här kommandot skapar databasen MySqlDwDb med tjänstmålet DW400 och låter databasen växa till en maximal storlek på 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Skapa en databas med sqlcmd

Alternativt kan du köra samma kommando med sqlcmd genom att köra följande i kommandotolken.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Standardsortering om inte ANNAT anges är COLLATE SQL_Latin1_General_CP1_CI_AS.  `MAXSIZE` kan vara mellan 250 GB och 240 TB.  `SERVICE_OBJECTIVE` kan vara mellan DW100 och DW2000 [DWU][].  En lista över alla giltiga värden finns i MSDN-dokumentationen för [SKAPA DATABAS][].  Både MAXSIZE och SERVICE_OBJECTIVE kan ändras med T-SQL-kommandot [ALTER DATABASE][].  Sorteringen för en databas kan inte ändras efter skapandet.   Var försiktig när du ändrar SERVICE_OBJECTIVE eftersom det orsakar en omstart av tjänster som avbryter alla pågående frågor.  En ändring av MAXSIZE startar inte om tjänsterna eftersom det bara är en enkel metadata-åtgärd.

## Nästa steg

När ditt SQL Data Warehouse är färdigetablerat, kan du [läsa in exempeldata][] eller kolla hur man [utvecklar][], [läser in][], eller [migrerar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[så här skapar du ett SQL Data Warehouse från Azure-portalen]: sql-data-warehouse-get-started-provision.md
[Fråga SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrerar]: sql-data-warehouse-overview-migrate.md
[utvecklar]: sql-data-warehouse-overview-develop.md
[läser in]: sql-data-warehouse-overview-load.md
[läsa in exempeldata]: sql-data-warehouse-load-sample-databases.md
[Skapa en logisk Azure SQL Database-server med Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Skapa en logisk Azure SQL Database-server med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[hur du kan skapa en resursgrupp]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installera Visual Studio och SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[SKAPA DATABAS]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Priser för SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Kostnadsfri utvärderingsversion av Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-krediter]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Sep16_HO3-->


