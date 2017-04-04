---
title: Skapa ett SQL Data Warehouse med TSQL | Microsoft Docs
description: "Lär dig hur du skapar ett Azure SQL Data Warehouse med TSQL"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 836d72e32e54ecef9691b55214766a1fc3ff9701
ms.lasthandoff: 12/06/2016


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Skapa en SQL Data Warehouse-databas med hjälp av Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Azure-portalen](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

I den här artikeln visas hur du skapar ett SQL Data Warehouse med T-SQL.

## <a name="prerequisites"></a>Krav
Du behöver följande för att komma igång:

* **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][Azure Free Trial] eller [MSDN Azure-krediter][MSDN Azure Credits] för att skapa ett konto.
* **Azure SQL Server**: Mer information finns i [Skapa en logisk Azure SQL Database-server med Azure Portal][Create an Azure SQL Database logical server with the Azure Portal] och [Skapa en logisk Azure SQL Database-server med PowerShell][Create an Azure SQL Database logical server with PowerShell].
* **Resursgrupp**: Använd antingen samma resursgrupp som din Azure SQL-server eller se [hur du skapar en resursgrupp][how to create a resource group].
* **Körningsmiljö för T-SQL**: Du kan använda [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] eller [SSMS][SSMS] för att köra T-SQL.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information om priser finns i [Priser för SQL Data Warehouse][SQL Data Warehouse pricing].
>
>

## <a name="create-a-database-with-visual-studio"></a>Skapa en databas med Visual Studio
Om du precis har kommit igång med Visual Studio läser du artikeln [Fråga SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  För att starta, öppnar du SQL Server Object Explorer i Visual Studio och ansluter till servern där din SQL Data Warehouse-databas kommer ligga.  När du väl är ansluten, kan du skapa ett SQL Data Warehouse genom att köra följande SQL-kommando mot **huvud**-databasen.  Det här kommandot skapar databasen MySqlDwDb med tjänstmålet DW400 och låter databasen växa till en maximal storlek på 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Skapa en databas med sqlcmd
Alternativt kan du köra samma kommando med sqlcmd genom att köra följande i kommandotolken.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Standardsortering om inte ANNAT anges är COLLATE SQL_Latin1_General_CP1_CI_AS.  `MAXSIZE` kan vara mellan 250 GB och 240 TB.  `SERVICE_OBJECTIVE` kan vara mellan DW100 och DW2000 [DWU][DWU].  En lista över alla giltiga värden finns i MSDN-dokumentationen för [CREATE DATABASE][CREATE DATABASE].  Både MAXSIZE och SERVICE_OBJECTIVE kan ändras med T-SQL-kommandot [ALTER DATABASE][ALTER DATABASE].  Sorteringen för en databas kan inte ändras efter skapandet.   Var försiktig när du ändrar SERVICE_OBJECTIVE eftersom det orsakar en omstart av tjänster som avbryter alla pågående frågor.  En ändring av MAXSIZE startar inte om tjänsterna eftersom det bara är en enkel metadata-åtgärd.

## <a name="next-steps"></a>Nästa steg
När ditt SQL Data Warehouse är färdigetablerat kan du [läsa in exempeldata][load sample data] eller lära dig hur du [utvecklar][develop], [läser in][load] eller [migrerar][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

