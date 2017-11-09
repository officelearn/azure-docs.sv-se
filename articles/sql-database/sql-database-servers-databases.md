---
title: Skapa och hantera Azure SQL-servrar och databaser | Microsoft Docs
description: "Lär dig mer om Azure SQL Database-server och databasbegrepp och skapa och hantera servrar och databaser."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 469db4f3faf12cbd778f18b7bc74ec6b86b412c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Skapa och hantera Azure SQL Database-servrar och databaser

En Azure SQL database är en hanterad databas i Microsoft Azure som skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med en definierad uppsättning [beräkning och lagring resurser för olika arbetsbelastningar](sql-database-service-tiers.md). En Azure SQL database är associerad med en logisk server från Azure SQL Database som skapas i en viss Azure-region. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>En Azure SQL database kan vara en enskild, grupperade eller partitionerade databas

En Azure SQL database kan vara:

- En [enskild databas](sql-database-single-database-resources.md) med en egen uppsättning resurser
- En del av en [elastisk pool](sql-database-elastic-pool.md) som delar en uppsättning resurser
- En del av en [utskalad uppsättning delade databaser](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), som kan vara enskilda databaser eller databaser i en pool
- En del av en uppsättning databaser som ingår i ett [SaaS-designmönster för flera klienter](sql-database-design-patterns-multi-tenancy-saas-applications.md), vars databaser kan vara enskilda databaser eller databaser i en pool (eller båda) 

> [!TIP]
> För giltiga databasnamn, se [databasidentifierare](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). 
>
 
- Standarddatabassorteringen som används av Microsoft Azure SQL Database är **SQL_LATIN1_GENERAL_CP1_CI_AS**, där **LATIN1_GENERAL** är engelska (USA), **CP1** är teckentabell 1252, **CI** är skiftlägeskänslig och **AS** är accentkänslig. Mer information om hur du konfigurerar sorteringen finns i [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- Microsoft Azure SQL Database stöder tabelldata dataström (TDS) protokollet klientversionen 7.3 eller senare.
- TCP/IP-anslutningar tillåts.

## <a name="what-is-an-azure-sql-logical-server"></a>Vad är en logisk Azure SQL-server?

En logisk server som fungerar som en central administrativ plats för flera databaser, inklusive [elastiska pooler](sql-database-elastic-pool.md) [inloggningar](sql-database-manage-logins.md), [regler i brandväggen](sql-database-firewall-configure.md), [granskning regler](sql-database-auditing.md), [hot principer](sql-database-threat-detection.md), och [redundans grupper](sql-database-geo-replication-overview.md). En logisk server kan vara i en annan region än dess resursgruppen. Den logiska servern måste finnas innan du kan skapa Azure SQL-databas. Alla databaser på en server som skapas i samma region som den logiska servern. 


> [!IMPORTANT]
> I SQL Database är en server en logisk konstruktion som skiljer sig från en SQL Server-instans, som du kanske är bekant med i den lokala miljön. Mer specifikt ger SQL Database-tjänsten inga garantier avseende platsen för databaserna i förhållande till deras logiska servrar och exponerar inga funktioner eller åtkomst på instansnivå.
> 

När du skapar en logisk server kan ange du en server inloggningskonto och lösenord som har administrativ behörighet till master-databasen på den servern och alla databaser som skapas på servern. Det här första kontot är ett konto för SQL-inloggning. Azure SQL Database stöder SQL-autentisering och Azure Active Directory-autentisering för autentisering. Information om inloggning och autentisering finns [hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md). Windows-autentisering stöds inte. 

> [!TIP]
> Giltig resurs grupp- och servernamnen finns [namngivning av regler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

En logisk Azure Database-server:

- Skapas i en Azure-prenumeration, men kan flyttas till en annan prenumeration tillsammans med de resurser som den innehåller.
- Är den överordnade resursen för databaser, elastiska pooler och informationslager.
- Ger ett namnområde för databaser och elastiska pooler datalager
- Är en logisk behållare med starka livstid semantik - ta bort en server och tar bort den inneslutna databaser och elastiska pooler datalager
- Deltar i [Azure rollbaserad åtkomstkontroll (RBAC)](/active-directory/role-based-access-control-what-is) -databaser och elastiska pooler datalager i en server ärver behörigheter från servern
- Är ett högsta element för identiteten för databaser och elastiska pooler datalager för Azure-resurs hanteringsändamål (se URL-schemat för databaser och pooler)
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för databasåtkomst (<serverName>.database.windows.net)
- Ger åtkomst till metadata för inneslutna resurser via DMV:er genom att ansluta till en huvuddatabas. 
- Ger omfång för principer för hantering som gäller för dess databaser - inloggningar, brandvägg, granska, hot identifiering osv. 
- Begränsas av en kvot i den överordnade prenumerationen (sex servrar per prenumeration som standard - [finns prenumeration begränsar här](../azure-subscription-service-limits.md))
- Innehåller omfattningen för databaskvoten och DTU-kvot för de resurser som den innehåller (till exempel 45 000 DTU)
- Är versionshantering omfånget för funktioner på befintliga resurser 
- Huvudkontoinloggningar på servernivå kan hantera alla databaser på en server.
- Kan innehålla inloggningar som liknar de i instanser av SQL Server i din lokala miljö som har åtkomst till en eller flera databaser på servern, samt kan beviljas begränsade administrativa rättigheter. Mer information finns i avsnittet om [inloggningar](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Azure SQL-databaser som skyddas av Brandvägg för SQL-databas

För att skydda dina data, en [SQL Database-brandvägg](sql-database-firewall-configure.md) förhindrar all åtkomst till databasservern eller någon av dess databaser från utanför din anslutning till servern direkt via Azure-prenumeration anslutningen. Om du vill aktivera ytterligare anslutningar måste du [skapa brandväggsregler för en eller flera](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Skapa och hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Azure-portalen

Du kan skapa resursgrupp i Azure SQL-databasen i förväg eller när du skapar du själva servern. Det finns flera metoder för att hämta till ett nytt SQL server-format, antingen genom att skapa en ny SQLServer eller som en del av att skapa en ny databas. 

### <a name="create-a-blank-sql-server-logical-server"></a>Skapa en tom SQLServer (logisk server)

Skapa en Azure SQL Database-server (utan en databas) med den [Azure-portalen](https://portal.azure.com), navigera till ett tomt formulär för SQL server (logisk server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Skapa en tom eller exempel SQL-databas

Skapa en Azure SQL database med hjälp av den [Azure-portalen](https://portal.azure.com), navigera till ett tomt formulär för SQL-databasen och ange den begärda informationen. Du kan skapa Azure SQL-databas resursgrupp och logisk server i förväg eller när du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserat på Adventure Works med 

  ![skapa databas-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för din databas finns [tjänstnivåer](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Hantera en befintlig SQLServer

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder - exempel från och med den specifika SQL-databas i **SQL-servrar** sidan eller **alla resurser** sidan. 

Om du vill hantera en befintlig databas, gå till den **SQL-databaser** och klicka på den databas du vill hantera. Följande skärmbild visar hur du börjar ställa in en servernivå Brandvägg för en databas från den **översikt** sida för en databas. 

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Om du vill konfigurera egenskaper för prestanda för en databas finns [tjänstnivåer](sql-database-service-tiers.md).
>

> [!TIP]
> En självstudiekurs i Azure portal Snabbstart finns [skapa en Azure SQL database i Azure portal](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Hantera Azure SQL-servrar, databaser och brandväggar med PowerShell

Använd följande PowerShell-cmdlets för att skapa och hantera Azure SQL server-databaser och brandväggar med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Skapa och hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en databas |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Ta bort-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Tar bort en databas|
|[Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Skapar en resursgrupp]
|[Ny AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Skapar en server|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Returnerar information om servrar|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Ändrar egenskaperna för en server|
|[Ta bort AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Tar bort en server|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| Ny AzureRmSqlServerVirtualNetworkRule | Skapar en [ *virtuellt nätverk regeln*](sql-database-vnet-service-endpoint-rule-overview.md), baserat på ett undernät som är en tjänstslutpunkt för virtuellt nätverk. |

> [!TIP]
> En PowerShell Snabbstartsguide finns [skapa en enda Azure SQL-databas med hjälp av PowerShell](sql-database-get-started-portal.md). PowerShell-exempelskript, se [Använd PowerShell för att skapa en Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-powershell.md) och [Övervakare och skala en enskild SQL-databas med hjälp av PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Azure CLI

Skapa och hantera Azure SQL server-databaser och brandväggar med den [Azure CLI](/cli/azure/overview), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Skapa och hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql-databas](/cli/azure/sql/db#az_sql_db_create) |Skapar en databas|
|[AZ sql db-lista](/cli/azure/sql/db#az_sql_db_list)|Visar en lista över alla databaser och datalager i en server eller alla databaser i en elastisk pool|
|[AZ sql db lista-versioner](/cli/azure/sql/db#az_sql_db_list_editions)|Visar tillgängliga mål och Lagringsgränser|
|[AZ sql db lista-användningsområden](/cli/azure/sql/db#az_sql_db_list_usages)|Returnerar databasen användningsområden|
|[AZ sql db visa](/cli/azure/sql/db#az_sql_db_show)|Hämtar ett databasen eller data warehouse|
|[AZ sql db-uppdateringen](/cli/azure/sql/db#az_sql_db_update)|Uppdaterar en-databas|
|[AZ sql db bort](/cli/azure/sql/db#az_sql_db_delete)|Tar bort en databas|
|[Skapa AZ grupp](/cli/azure/group#az_group_create)|Skapar en resursgrupp|
|[Skapa AZ SQLServer](/cli/azure/sql/server#az_sql_server_create)|Skapar en server|
|[AZ sql server-lista](/cli/azure/sql/server#az_sql_server_list)|Visar servrar|
|[AZ sql server lista-användningsområden](/cli/azure/sql/server#az_sql_server_list-usages)|Returnerar servern användningsområden|
|[Visa för AZ sql server](/cli/azure/sql/server#az_sql_server_show)|Hämtar en server|
|[AZ sql server-uppdatering](/cli/azure/sql/server#az_sql_server_update)|Uppdaterar en server|
|[ta bort AZ sql-server](/cli/azure/sql/server#az_sql_server_delete)|Tar bort en server|
|[Skapa AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Skapar en brandväggsregel|
|[AZ sql server-brandväggsregel lista](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Visar en lista över brandväggsregler på en server|
|[AZ sql server-brandväggsregel visa](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Visar information om en brandväggsregel|
|[uppdatering av AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uppdaterar en brandväggsregel|
|[ta bort AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Tar bort en brandväggsregel|

> [!TIP]
> Läs en Azure CLI Snabbstartsguide [skapa en enda Azure SQL-databas med hjälp av Azure CLI](sql-database-get-started-cli.md). Azure CLI exempelskript finns [Använd CLI för att skapa en Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md) och [Använd CLI för att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Transact-SQL

Använd följande T-SQL-kommandon för att skapa och hantera Azure SQL server-databaser och brandväggar med Transact-SQL. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. För att hantera elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

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
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar förbrukning av CPU, i/o och minne för en Azure SQL Database-databas. Det finns en rad för var 15: e sekund, även om det finns ingen aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagring data för en Azure SQL Database. Data som samlas in och sammanställs inom fem-minuters mellanrum.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för SQL Database connectivity databashändelser, ger en översikt av databasen anslutning framgångar och misslyckanden. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade Azure SQL Database-databasanslutningar anslutningsfel och deadlocks. Du kan använda den här informationen för att övervaka och felsöka din Databasaktivitet med SQL-databas.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar servernivå brandväggsinställningar för SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den huvudsaklig inloggningen på servernivå. En brandväggsregel på servernivå kan bara skapas med hjälp av Transact-SQL efter den första brandväggsregeln på servernivå har skapats av en användare med Azure-behörighet|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om servernivå brandväggsinställningar som är associerade med Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort servernivå brandväggsinställningar från din SQL Database-server. Den här lagrade proceduren är endast tillgänglig i master-databasen till den huvudsaklig inloggningen på servernivå.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar databasnivå brandväggsregler för din Azure SQL Database eller SQL Data Warehouse. Databasens brandväggsregler kan konfigureras för master-databasen och databaserna på SQL-databas. Databasens brandväggsregler är användbara när du använder finns databasanvändare. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om databasnivå brandväggsinställningar som är associerade med Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort databasnivå brandväggsinställningen från din Azure SQL Database eller SQL Data Warehouse. |


> [!TIP]
> Snabbstartsguide med SQL Server Management Studio på Microsoft Windows, se [Azure SQL Database: Använd SQL Server Management Studio för att ansluta och fråga efter data](sql-database-connect-query-ssms.md). En självstudiekurs med hjälp av Visual Studio-koden i macOS, Linux eller Windows, se [Azure SQL Database: Använd Visual Studio Code kan ansluta och fråga efter data](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Hantera Azure SQL-servrar, databaser och brandväggar med hjälp av REST-API

Använd dessa REST API-begäranden för att skapa och hantera Azure SQL server, databaser och brandväggar.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar – skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar – ta bort](/rest/api/sql/servers/delete)|Tar bort en SQLServer.|
|[Servrar – Get](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar – lista](/rest/api/sql/servers/list)|Returnerar en lista över servrar.|
|[Servrar – lista med resursgrupp](/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista över servrar i en resursgrupp.|
|[Servrar – uppdatering](/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Servrar – Sql](/rest/api/sql/servers%20-%20sql)|Anger om en resurs kan skapas med det angivna namnet.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en recommented elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en recommented elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brandväggen regler - skapa eller uppdatera](/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brandväggsregel.|
|[Brandväggsregler – ta bort](/rest/api/sql/firewallrules/delete)|Tar bort en brandväggsregel.|
|[Brandväggsregler - Get](/rest/api/sql/firewallrules/get)|Hämtar en brandväggsregel.|
|[Brandväggsregler – lista av servern](/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista med brandväggsregler.|

## <a name="next-steps"></a>Nästa steg

- Läs om poolning databaser med elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
- Information om Azure SQL Database-tjänsten finns [vad är SQL Database?](sql-database-technical-overview.md).
- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-cloud-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
