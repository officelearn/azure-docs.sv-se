---
title: Skapa och hantera elastiska pooler - Azure SQL database | Microsoft Docs
description: Skapa och hantera Azure SQL elastiska pooler.
keywords: flera databaser, databasresurser, databasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313327"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Skapa och hantera elastiska pooler i Azure SQL Database

Du avgör mängden resurser som den elastiska poolen som krävs för att hantera belastningen på databaserna och mängden resurser för varje databas, grupperade med en elastisk pool. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure-portalen: hantera elastiska pooler och grupperade databaser

Alla inställningar för programpool kan hittas på en plats: den **konfigurera pool** bladet. Hitta en elastisk pool i portalen och klicka på för att få här **konfigurera pool** högst upp på bladet eller resurs-menyn till vänster.

Du kan välja valfri kombination av följande ändringar och spara dem i en batch härifrån:
1. Ändra tjänstnivån för poolen
2. Skala prestanda (DTU eller vCores) och lagring upp eller ned
3. Lägg till eller ta bort databaser från poolen
4. Ange en minut (garanteras) samt högsta antal prestanda gränsen för databaserna i pooler
5. Granska sammanfattningen av kostnaden för att visa ändringar i fakturan på grund av nya alternativ

![Elastisk pool configuration bladet](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Hantera elastiska pooler och grupperade databaser 

Om du vill skapa och hantera SQL-databas elastiska pooler och grupperade databaser med Azure PowerShell, Använd följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> PowerShell-exempelskript, se [skapa elastiska pooler och flytta databaser mellan pooler och från en pool med PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Skapar en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Hämtar elastiska pooler och deras värden på en logisk SQLServer.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Ändrar egenskaper för en elastisk databaspool på en logisk SQLServer. Till exempel använda den **StorageMB** egenskapen att ändra maximal lagringskapacitet för en elastisk pool.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Tar bort en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool på en logisk SQLServer.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas i, slut på eller mellan elastiska pooler.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Tar bort en databas.|


> [!TIP]
> Skapandet av många databaser i en elastisk pool kan ta tid när det görs med portalen eller PowerShell-cmdletar som skapar bara en enskild databas åt gången. Om du vill automatisera skapande i en elastisk pool, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Hantera elastiska pooler och grupperade databaser

Skapa och hantera SQL-databas elastiska pooler med den [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Azure CLI exempelskript finns [Använd CLI för att flytta en Azure SQL database i en elastisk pool SQL](scripts/sql-database-move-database-between-pools-cli.md) och [Använd Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Skapar en elastisk pool.|
|[AZ sql elastisk pool lista](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Returnerar en lista över elastiska pooler i en server.|
|[AZ sql elastisk pool lista-databaser](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[AZ sql elastisk pool lista-versioner](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Även tillgängliga poolen DTU inställningar, Lagringsgränser och per databasinställningarna. För att minska detaljnivå ytterligare Lagringsgränser och per databas inställningar döljs som standard.|
|[AZ sql elastisk pool uppdateringen](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Uppdaterar en elastisk pool.|
|[ta bort AZ sql-elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Hantera grupperade databaser

Skapa och flytta databaser i befintliga elastiska pooler eller att returnera information om en SQL Database-elastisk pool med Transact-SQL, Använd följande T-SQL-kommandon. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database-elastisk pool med Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda av DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas i, slut på eller mellan elastiska pooler.|
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resurs användningsstatistik för elastiska databaspooler i en logisk server. För varje elastisk databaspool att det finns en rad för varje 15 sekunder reporting fönstret (fyra rader per minut). Detta inkluderar CPU, IO, Log, användningen av lagringsutrymme och samtidiga begäran-session användning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivån), tjänstmålet (prisnivån) och namn på elastisk pool, för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. För Azure SQL Data Warehouse, måste du vara ansluten till master-databasen.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST-API: Hantera elastiska pooler och grupperade databaser

Använd dessa REST API-begäranden för att skapa och hantera SQL-databas elastiska pooler och grupperade databaser.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler - ta bort](/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler - Get](/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler - lista av servern](/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista över elastiska pooler i en server.|
|[Elastiska pooler - uppdatering](/rest/api/sql/elasticpools/update)|Uppdaterar en befintlig elastisk pool.|
|[Rekommenderade elastiska pooler - Get](/rest/api/sql/recommendedelasticpools/get)|Hämtar en rekommenderad elastisk pool.|
|[Rekommenderade elastiska pooler - lista av servern](/rest/api/sql/recommendedelasticpools/listbyserver)|Returnerar rekommenderade elastiska pooler.|
|[Rekommenderade elastiska pooler - listan mått](/rest/api/sql/recommendedelasticpools/listmetrics)|Returnerar rekommenderad elastisk pool mått.|
|[Elastisk Pool aktiviteter](/rest/api/sql/elasticpoolactivities)|Returnerar elastisk pool aktiviteter.|
|[Databasaktiviteter elastisk Pool](/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser i en elastisk pool.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en rekommenderad elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en rekommenderad elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Se en video [Microsoft Virtual Academy video kursen på Azure SQL Database-elastisk funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med elastiska pooler finns [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).
