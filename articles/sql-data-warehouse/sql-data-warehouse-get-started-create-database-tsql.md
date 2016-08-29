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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Skapa en SQL Data Warehouse-databas med hjälp av Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Den här artikeln visar hur du skapar en SQL Data Warehouse-databas med Transact-SQL (T-SQL).

## Förutsättningar
Se till att du uppfyller följande förutsättningar innan du startar.

- **Azure-konto**: se [Kostnadsfri utvärderingsversion av Azure][] eller [MSDN Azure-krediter][] för att skapa ett konto.
- **V12 Azure SQL Server**: se [Skapa en logisk Azure SQL Database-server med Azure Portal][] eller [Skapa en logisk Azure SQL Database-server med PowerShell][].
- **Resursgruppnamn**: Använd antingen samma resursgrupp som din V12 Azure SQL-server eller se [resursgrupper][] för att skapa en ny resursgrupp.
- **Visual Studio med SQL Server Data Tools**: För installationsinstruktioner, se [installera Visual Studio och SSDT][].

> [AZURE.NOTE] Att skapa ett nytt SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Se [Priser för SQL Data Warehouse][] för mer information om priser.

## Skapa en databas med Visual Studio

Om du precis kommit igång med Visual Studio, kan du se artikeln [anslut till SQL Data Warehouse med Visual Studio][].  För att starta, öppnar du SQL Server Object Explorer i Visual Studio och ansluter till servern där din SQL Data Warehouse-databas kommer ligga.  När du väl är ansluten, kan du skapa ett SQL Data Warehouse genom att köra följande SQL-kommando mot **huvud**-databasen.  Det här kommandot skapar databasen MySqlDwDb med tjänstmålet DW400 och låter databasen växa till en maximal storlek på 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Skapa en databas med sqlcmd

Alternativt kan du köra samma kommando med sqlcmd genom att köra följande i kommandotolken.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Parametrarna **MAXSIZE** och **SERVICE_OBJECTIVE** anger maximalt utrymme som databasen får använda på disken och vilka beräkningsresurser som allokeras till din Data Warehouse-instans.  Tjänstmålet är i grunden en allokering av cpu och minne som är linjärt skalbar efter DWU-storleken.  

MAXSIZE kan vara mellan 250 GB och 240 TB.  Tjänstmålet kan vara mellan DW100 och DW2000.  En fullständig lista över alla giltiga värden för MAXSIZE och SERVICE_OBJECTIVE finns i MSDN-dokumentationen för [CREATE DATABASE][].  Både MAXSIZE och SERVICE_OBJECTIVE kan också ändras med T-SQL-kommandot [ALTER DATABASE][].  Var försiktig när du ändrar SERVICE_OBJECTIVE eftersom det orsakar en omstart av tjänster som avbryter alla pågående frågor.  En ändring av MAXSIZE startar inte om tjänsterna eftersom det bara är en enkel metadata-åtgärd.

## Nästa steg
När ditt SQL Data Warehouse är färdigetablerat, kan du [läsa in exempeldata][] eller kolla hur man [utvecklar][], [läser in][], eller [migrerar][].

<!--Article references-->
[så här skapar du ett SQL Data Warehouse från Azure-portalen]: ./sql-data-warehouse-get-started-provision.md
[anslut till SQL Data Warehouse med Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migrerar]: ./sql-data-warehouse-overview-migrate.md
[utvecklar]: ./sql-data-warehouse-overview-develop.md
[läser in]: ./sql-data-warehouse-overview-load.md
[läsa in exempeldata]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Skapa en logisk Azure SQL Database-server med Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Skapa en logisk Azure SQL Database-server med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resursgrupper]: ../azure-portal/resource-group-portal.md
[installera Visual Studio och SSDT]: ./sql-data-warehouse-install-visual-studio.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Priser för SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Kostnadsfri utvärderingsversion av Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-krediter]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!----HONumber=Jun16_HO2-->


