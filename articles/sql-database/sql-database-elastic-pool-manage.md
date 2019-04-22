---
title: Hantera elastiska pooler – Azure SQL-databas | Microsoft Docs
description: Skapa och hantera elastiska Azure SQL-pooler.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: debf91f04cff3cb9705ebc5915e2e665679230a9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59267590"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Hantera elastiska pooler i Azure SQL Database

Du avgör mängden resurser som den elastiska poolen som krävs för att hantera arbetsbelastningen av dess databaser och mängden resurser för varje databas i pool med en elastisk pool.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure-portalen: Hantera elastiska pooler och databaser i en pool

Alla poolinställningar finns i ett och samma ställe: den **konfigurera pool** bladet. Om du vill ha här hittar du en elastisk pool i portalen och klicka på **konfigurera pool** antingen högst upp på bladet eller från resurs-menyn till vänster.

Härifrån kan du valfri kombination av följande ändringar och spara dem alla i en batch:

1. Ändra tjänstnivån för poolen
2. Skala prestanda (DTU eller vCores) och lagring uppåt eller nedåt
3. Lägga till eller ta bort databaser till och från poolen
4. Ange ett minsta (garanterad) och maximal prestandagräns för databaser i poolerna
5. Läser du sammanfattningen av kostnaden för att visa alla ändringar på din faktura på grund av nya alternativ

![Konfigurationsbladet för elastisk pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Hantera elastiska pooler och databaser i en pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

Använd följande PowerShell-cmdletar för att skapa och hantera SQL Database elastiska pooler och databaser i en pool med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell kan du läsa [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Om du vill skapa och hantera SQL Database-servrar för en elastisk pool, se [skapa och hantera SQL Database-servrar](sql-database-servers.md). För att skapa och hantera brandväggsregler, se [skapa och hantera brandväggsregler med hjälp av PowerShell](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell).

> [!TIP]
> Exempelskript för PowerShell, se [skapa elastiska pooler och flytta databaser mellan pooler och från en pool med PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [använda PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Skapar en elastisk pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hämtar elastiska pooler och deras värden.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändrar egenskaperna för en elastisk pool till exempel den **StorageMB** egenskapen att ändra det maximala lagringsutrymmet för en elastisk pool.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Tar bort en elastisk pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en ny databas i en befintlig pool eller som en enskild databas. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas till, från eller mellan elastiska pooler.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas.|

> [!TIP]
> Skapandet av flera databaser i en elastisk pool kan ta tid när det görs med portalen eller PowerShell-cmdletar som skapar bara en enskild databas i taget. Om du vill automatisera skapande i en elastisk pool, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Hantera elastiska pooler och databaser i en pool

Skapa och hantera elastiska pooler i SQL Database med de [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Exempelskript för Azure CLI, se [Använd CLI för att flytta en Azure SQL-databas i en SQL-databaspool](scripts/sql-database-move-database-between-pools-cli.md) och [används Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql elastic-pool](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Skapar en elastisk pool.|
|[AZ sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Returnerar en lista med elastiska pooler på en server.|
|[AZ sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[AZ sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Innehåller också tillgänglig pool-DTU inställningar, Lagringsgränser och per databasinställningar. För att minska detaljnivå ytterligare gränser och per databas inställningar döljs som standard.|
|[AZ sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Uppdaterar en elastisk pool.|
|[AZ sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Hantera databaser i en pool

Skapa och flytta databaser i befintliga elastiska pooler, eller för att returnera information om en elastisk pool med SQL-databas med Transact-SQL, Använd följande T-SQL-kommandon. Du kan skicka dessa kommandon med hjälp av Azure-portalen [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL -kommandon. Om du vill skapa och hantera brandväggsregler med hjälp av T-SQL, se [hantera brandväggsregler med hjälp av Transact-SQL](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database elastisk pool med hjälp av Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool, och du kan använda DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig pool eller som en enskild databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas till, från eller mellan elastiska pooler.|
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resource användningsstatistik för elastiska pooler i SQL-databasserver. För varje elastisk pool finns en rad för varje 15 sekund reporting fönstret (fyra rader per minut). Detta inkluderar CPU, IO, Log, lagringsanvändningen och samtidig begäran/sessionen utnyttjande av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivå), tjänstmålet (prisnivå) och namn på elastisk pool, om sådant finns, för en Azure SQL database eller en Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. Du måste vara ansluten till huvuddatabasen för för Azure SQL Data Warehouse.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST-API: Hantera elastiska pooler och databaser i en pool

Använd dessa REST API-begäranden för att skapa och hantera SQL Database elastiska pooler och databaser i en pool.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler – ta bort](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler – Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler – listan efter server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista med elastiska pooler på en server.|
|[Elastiska pooler – uppdatera](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Uppdaterar en befintlig elastisk pool.|
|[Elastisk pool-aktiviteter](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Returnerar elastisk pool aktiviteter.|
|[Elastisk pool databasaktiviteter](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser i en elastisk pool.|
|[Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista med elastisk pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser – listan efter server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser – uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med hjälp av elastiska pooler finns i [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).
