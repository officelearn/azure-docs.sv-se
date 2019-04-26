---
title: Skapa, hantera Azure SQL Database-servrar och enskilda databaser | Microsoft Docs
description: Läs mer om att skapa och hantera SQL Database-servrar och enskilda databaser.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: bcbed12940b7766d7ffb9e67b7c63931160ba9b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331748"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Skapa och hantera SQL Database-servrar och enskilda databaser i Azure SQL Database

Du kan skapa och hantera SQL Database-servrar och enskilda databaser med hjälp av Azure portal, PowerShell, Azure CLI, REST API och Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Azure-portalen: Hantera SQL Database-servrar och enskilda databaser

Du kan skapa resursgrupp för Azure SQL-databas förbereds i förväg eller när du skapar själva servern. Det finns flera metoder för att komma till ett nytt SQL server-format, antingen genom att skapa en ny SQLServer eller som del av att skapa en ny databas.

### <a name="create-a-blank-sql-database-server"></a>Skapa en tom SQL-databasserver

Att skapa en SQL Database-server med den [Azure-portalen](https://portal.azure.com), navigera till ett tomt formulär för SQL server (logisk server).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Skapa en tom eller exemplet SQL databas

Skapa en Azure SQL-databas med den [Azure-portalen](https://portal.azure.com)går du till en tom SQL Database-formuläret och anger önskad information. Du kan skapa Azure SQL-databasen resursgruppen och SQL Database-server förbereds i förväg eller när du skapar en enda databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserad på Adventure Works med

  ![skapa databas-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för din databas finns i [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

Om du vill skapa en hanterad instans [skapar en hanterad instans](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Hantera en befintlig SQL Database-server

Navigera till servern med ett antal metoder – till exempel från specifika SQL database-sidan, om du vill hantera en befintlig SQL Database-server i **SQL-servrar** sidan eller **alla resurser** sidan.

För att hantera en befintlig databas, gå till den **SQL-databaser** och klicka på den databas du vill hantera. Skärmbilden nedan visar hur du börjar ställa in en brandväggsregel på servernivå för en databas från den **översikt** för en databas.

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> För att konfigurera egenskaper för prestanda för en databas, se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
> [!TIP]
> Läs en Azure-portalen Snabbstart, [skapa en Azure SQL database i Azure-portalen](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: Hantera SQL Database-servrar och enskilda databaser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

Använd följande PowerShell-cmdletar för att skapa och hantera Azure SQL Database-servrar, enskild och delade databaser och brandväggar för SQL Database-servern med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell kan du läsa [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

> [!TIP]
> Exempelskript för PowerShell, se [Använd PowerShell för att skapa en enskild Azure SQL-databas och konfigurera en brandväggsregel för SQL Database-server](scripts/sql-database-create-and-configure-database-powershell.md) och [Övervakare och skalning av en SQL-databas med PowerShell med enkel](scripts/sql-database-monitor-and-scale-database-powershell.md) .

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en databas |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Skapar en resursgrupp|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Skapar en server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Returnerar information om servrar|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Ändrar egenskaperna för en server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Tar bort en server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| New-AzSqlServerVirtualNetworkRule | Skapar en [ *virtuell nätverksregel*](sql-database-vnet-service-endpoint-rule-overview.md), baserat på ett undernät som är en tjänstslutpunkt för virtuellt nätverk. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: Hantera SQL Database-servrar och enskilda databaser

Skapa och hantera Azure SQL server, databaser och brandväggar med [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Skapa och hantera elastiska pooler finns i [elastiska pooler](sql-database-elastic-pool.md).

> [!TIP]
> En Snabbstart för Azure CLI, se [skapa en enskild Azure SQL-databas med Azure CLI](sql-database-cli-samples.md). Exempelskript för Azure CLI, se [Använd CLI för att skapa en enskild Azure SQL-databas och konfigurera en brandväggsregel för SQL Database](scripts/sql-database-create-and-configure-database-cli.md) och [Använd CLI för att övervaka och skala en enskild Azure SQL-databas](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Skapar en databas|
|[AZ sql db list](/cli/azure/sql/db#az-sql-db-list)|Visar en lista över alla databaser och datalager i en server eller alla databaser i en elastisk pool|
|[AZ sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Visar tillgängliga mål och gränser|
|[AZ sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Returnerar databasen användningar|
|[AZ sql db show](/cli/azure/sql/db#az-sql-db-show)|Hämtar en databasen eller datalagret|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Uppdaterar en-databas|
|[AZ sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Tar bort en databas|
|[az group create](/cli/azure/group#az-group-create)|Skapar en resursgrupp|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Skapar en server|
|[AZ sql server list](/cli/azure/sql/server#az-sql-server-list)|Visar servrar|
|[AZ sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Returnerar servern användningar|
|[AZ sql server show](/cli/azure/sql/server#az-sql-server-show)|Hämtar en server|
|[uppdatering av AZ sql server](/cli/azure/sql/server#az-sql-server-update)|Uppdaterar en server|
|[AZ sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Tar bort en server|
|[Skapa AZ sql server firewall-rule](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Skapar en brandväggsregel|
|[AZ sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Visar en lista över brandväggsreglerna på en server|
|[AZ sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Visar information om en brandväggsregel|
|[AZ sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Uppdaterar en brandväggsregel|
|[AZ sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tar bort en brandväggsregel|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: Hantera SQL Database-servrar och enskilda databaser

Använd följande T-SQL-kommandon för att skapa och hantera Azure SQL server, databaser och brandväggar med Transact-SQL. Du kan skicka dessa kommandon med hjälp av Azure-portalen [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL -kommandon. Hantera elastiska pooler finns i [elastiska pooler](sql-database-elastic-pool.md).

> [!TIP]
> En Snabbstart med hjälp av SQL Server Management Studio på Microsoft Windows, se [Azure SQL Database: Använda SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md). En Snabbstart med hjälp av Visual Studio Code på macOS, Linux eller Windows, se [Azure SQL Database: Använd Visual Studio Code för att ansluta och fråga efter data](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Skapar en ny databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL-databas)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Ändrar en Azure SQL database. |
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivå), tjänstmålet (prisnivå) och namn på elastisk pool, om sådant finns, för en Azure SQL database eller en Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. Du måste vara ansluten till huvuddatabasen för för Azure SQL Data Warehouse.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU, IO och minne i samband med en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem minuter långa intervall.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för SQL Database connectivity databashändelser, ger en översikt av databasen anslutning lyckade och misslyckade. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade anslutningar i Azure SQL Database-databas, anslutningsfel och låsningar. Du kan använda den här informationen för att spåra och felsöka din Databasaktivitet med SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsinställningarna på servernivå för din SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den primära inloggningen på servernivå. En brandväggsregel på servernivå kan bara skapas med hjälp av Transact-SQL efter den första brandväggsregeln på servernivå har skapats av en användare med Azure-behörighet|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om inställningarna för brandväggen på servernivå som är associerade med Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningar på servernivå från din SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den primära inloggningen på servernivå.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsreglerna på databasnivå för din Azure SQL Database eller SQL Data Warehouse. Database-brandväggsregler kan konfigureras för master-databasen och för användardatabaser på SQL-databas. Database-brandväggsregler är användbara när du använder finns databasanvändare. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om inställningarna för brandväggen på databasnivå som är associerade med Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brandväggsregel på databasnivå inställningen från Azure SQL Database eller SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST-API: Hantera SQL Database-servrar och enskilda databaser

Om du vill skapa och hantera Azure SQL server, databaser och brandväggar, kan du använda dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar – ta bort](https://docs.microsoft.com/rest/api/sql/servers/delete)|Tar bort en SQLServer.|
|[Servrar – Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar – lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Returnerar en lista över servrar i en prenumeration.|
|[Servrar – listan efter resursgrupp](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista över servrar i en resursgrupp.|
|[Servrar - uppdatering](https://docs.microsoft.com/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – ta bort](https://docs.microsoft.com/rest/api/sql/databases/delete)|Tar bort en databas.|
|[Databaser – Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista med elastisk pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser – listan efter server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser – uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brandväggsregler – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brandväggsregel.|
|[Brandväggsregler – ta bort](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Tar bort en brandväggsregel.|
|[Brandväggsregler - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Hämtar en brandväggsregel.|
|[Brandväggsregler - listan efter server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista med brandväggsregler.|

## <a name="next-steps"></a>Nästa steg

- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
