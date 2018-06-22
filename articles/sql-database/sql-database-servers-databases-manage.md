---
title: Skapa, hantera Azure SQL-servrar och enskilda databaser | Microsoft Docs
description: Lär dig mer om att skapa och hantera logiska servrar och enskilda databaser.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 918cfd0257c82e84451e07ef904dbda331f47b95
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313313"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Skapa och hantera logiska servrar och enskilda databaser i Azure SQL Database 

Du kan skapa och hantera Azure SQL-databaser logiska servrar och enskilda databaser med hjälp av Azure-portalen, PowerShell, Azure CLI, REST-API och Transact-SQL.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Azure-portalen: hantera logiska servrar och databaser

Du kan skapa resursgrupp i Azure SQL-databasen i förväg eller när du skapar du själva servern. Det finns flera metoder för att hämta till ett nytt SQL server-format, antingen genom att skapa en ny SQLServer eller som en del av att skapa en ny databas. 

### <a name="create-a-blank-sql-server-logical-server"></a>Skapa en tom SQLServer (logisk server)

Skapa en Azure SQL Database-server (utan en databas) med den [Azure-portalen](https://portal.azure.com), navigera till ett tomt formulär för SQL server (logisk server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Skapa en tom eller exempel SQL-databas

Skapa en Azure SQL database med hjälp av den [Azure-portalen](https://portal.azure.com), navigera till ett tomt formulär för SQL-databasen och ange den begärda informationen. Du kan skapa Azure SQL-databas resursgrupp och logisk server i förväg eller när du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserat på Adventure Works med 

  ![skapa databas-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för din databas finns [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).

Om du vill skapa en instans för hanterade finns [skapa en instans som hanteras](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>Hantera en befintlig SQLServer

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder - exempel från och med den specifika SQL-databas i **SQL-servrar** sidan eller **alla resurser** sidan. 

Om du vill hantera en befintlig databas, gå till den **SQL-databaser** och klicka på den databas du vill hantera. Följande skärmbild visar hur du börjar ställa in en servernivå Brandvägg för en databas från den **översikt** sida för en databas. 

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Om du vill konfigurera egenskaper för prestanda för en databas finns [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).
>

> [!TIP]
> Ett Azure portal Snabbstart Se [skapa en Azure SQL database i Azure portal](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell: Hantera logiska servrar och databaser

Använd följande PowerShell-cmdlets för att skapa och hantera Azure SQL server-databaser och brandväggar med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

> [!TIP]
> En PowerShell-Snabbstart Se [skapa en enda Azure SQL-databas med hjälp av PowerShell](sql-database-get-started-portal.md). PowerShell-exempelskript, se [Använd PowerShell för att skapa en Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-powershell.md) och [Övervakare och skala en enskild SQL-databas med hjälp av PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en databas |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Tar bort en databas|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Skapar en resursgrupp|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Skapar en server|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Returnerar information om servrar|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Ändrar egenskaperna för en server|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Tar bort en server|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| New-AzureRmSqlServerVirtualNetworkRule | Skapar en [ *virtuellt nätverk regeln*](sql-database-vnet-service-endpoint-rule-overview.md), baserat på ett undernät som är en tjänstslutpunkt för virtuellt nätverk. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: Hantera logiska servrar och databaser

Skapa och hantera Azure SQL server-databaser och brandväggar med [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Skapa och hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

> [!TIP]
> En Azure CLI-Snabbstart Se [skapa en enda Azure SQL-databas med hjälp av Azure CLI](sql-database-get-started-cli.md). Azure CLI exempelskript finns [Använd CLI för att skapa en Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md) och [Använd CLI för att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Skapar en databas|
|[AZ sql db-lista](/cli/azure/sql/db#az_sql_db_list)|Visar en lista över alla databaser och datalager i en server eller alla databaser i en elastisk pool|
|[AZ sql db lista-versioner](/cli/azure/sql/db#az_sql_db_list_editions)|Visar tillgängliga mål och Lagringsgränser|
|[AZ sql db lista-användningsområden](/cli/azure/sql/db#az_sql_db_list_usages)|Returnerar databasen användningsområden|
|[AZ sql db visa](/cli/azure/sql/db#az_sql_db_show)|Hämtar ett databasen eller data warehouse|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Uppdaterar en-databas|
|[AZ sql db bort](/cli/azure/sql/db#az_sql_db_delete)|Tar bort en databas|
|[az group create](/cli/azure/group#az_group_create)|Skapar en resursgrupp|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Skapar en server|
|[AZ sql server-lista](/cli/azure/sql/server#az_sql_server_list)|Visar servrar|
|[AZ sql server lista-användningsområden](/cli/azure/sql/server#az_sql_server_list_usages)|Returnerar servern användningsområden|
|[Visa för AZ sql server](/cli/azure/sql/server#az_sql_server_show)|Hämtar en server|
|[AZ sql server-uppdatering](/cli/azure/sql/server#az_sql_server_update)|Uppdaterar en server|
|[ta bort AZ sql-server](/cli/azure/sql/server#az_sql_server_delete)|Tar bort en server|
|[Skapa AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Skapar en brandväggsregel|
|[AZ sql server-brandväggsregel lista](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Visar en lista över brandväggsregler på en server|
|[AZ sql server-brandväggsregel visa](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Visar information om en brandväggsregel|
|[uppdatering av AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uppdaterar en brandväggsregel|
|[ta bort AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Tar bort en brandväggsregel|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: Hantera logiska servrar och databaser

Använd följande T-SQL-kommandon för att skapa och hantera Azure SQL server-databaser och brandväggar med Transact-SQL. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. För att hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).


> [!TIP]
> En Snabbstart med SQL Server Management Studio på Microsoft Windows, se [Azure SQL Database: Använd SQL Server Management Studio för att ansluta och fråga efter data](sql-database-connect-query-ssms.md). En Snabbstart med hjälp av Visual Studio-koden i macOS, Linux eller Windows, se [Azure SQL Database: Använd Visual Studio Code kan ansluta och fråga efter data](sql-database-connect-query-vscode.md).

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.
>

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL-databas)](/sql/t-sql/statements/alter-database-azure-sql-database) |Ändrar en Azure SQL database. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Ändrar ett Azure SQL Data Warehouse.|
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivån), tjänstmålet (prisnivån) och namn på elastisk pool, för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. För Azure SQL Data Warehouse, måste du vara ansluten till master-databasen.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar förbrukning av CPU, IO och minne för en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem-minuters mellanrum.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för SQL Database connectivity databashändelser, ger en översikt av databasen anslutning framgångar och misslyckanden. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade Azure SQL Database-databasanslutningar anslutningsfel och deadlocks. Du kan använda den här informationen för att övervaka och felsöka din Databasaktivitet med SQL-databas.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar servernivå brandväggsinställningar för SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den huvudsaklig inloggningen på servernivå. En brandväggsregel på servernivå kan bara skapas med hjälp av Transact-SQL efter den första brandväggsregeln på servernivå har skapats av en användare med Azure-behörighet|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om servernivå brandväggsinställningar som är associerade med Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort servernivå brandväggsinställningar från din SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den huvudsaklig inloggningen på servernivå.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar databasnivå brandväggsregler för din Azure SQL Database eller SQL Data Warehouse. Databasens brandväggsregler kan konfigureras för master-databasen och databaserna på SQL-databas. Databasens brandväggsregler är användbara när du använder finns databasanvändare. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om databasnivå brandväggsinställningar som är associerade med Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort databasnivå brandväggsinställningen från din Azure SQL Database eller SQL Data Warehouse. |



## <a name="rest-api-manage-logical-servers-and-databases"></a>REST-API: Hantera logiska servrar och databaser

Om du vill skapa och hantera Azure SQL server, databaser och brandväggar, kan du använda dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar – skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar – ta bort](/rest/api/sql/servers/delete)|Tar bort en SQLServer.|
|[Servrar – Get](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar – lista](/rest/api/sql/servers/list)|Returnerar en lista över servrar.|
|[Servrar – lista med resursgrupp](/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista över servrar i en resursgrupp.|
|[Servrar – uppdatering](/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en recommented elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en rekommenderad elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brandväggen regler - skapa eller uppdatera](/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brandväggsregel.|
|[Brandväggsregler – ta bort](/rest/api/sql/firewallrules/delete)|Tar bort en brandväggsregel.|
|[Brandväggsregler - Get](/rest/api/sql/firewallrules/get)|Hämtar en brandväggsregel.|
|[Brandväggsregler – lista av servern](/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista med brandväggsregler.|

## <a name="next-steps"></a>Nästa steg

- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-cloud-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
