---
title: Servrar
description: Lär dig mer om Azure SQL Database-servrar och deras hantering.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647177"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL Database-servrar och deras hantering

## <a name="what-is-an-azure-sql-database-server"></a>Vad är en Azure SQL Database-server

En SQL Database-server är en logisk konstruktion som fungerar som en central administrativ punkt för flera enstaka eller [poolade](sql-database-elastic-pool.md) databaser, [inloggningar,](sql-database-manage-logins.md) [brandväggsregler,](sql-database-firewall-configure.md) [granskningsregler,](sql-database-auditing.md) [principer för hotidentifiering](sql-database-threat-detection.md)och [redundansgrupper](sql-database-auto-failover-group.md) En SQL Database-server kan finnas i en annan region än sin resursgrupp. SQL Database-servern måste finnas innan du kan skapa Azure SQL-databasen. Alla databaser som hanteras av en SQL Database-server skapas inom samma region som SQL Database-servern.

En SQL Database-server skiljer sig från en SQL Server-instans som du kanske är bekant med i den lokala världen. I den sql-databastjänsten finns inga garantier för var databaserna finns i förhållande till SQL Database-servern som hanterar dem och inga åtkomster eller funktioner på instansnivå visas. Däremot är instansdatabaserna i en hanterad instans alla samlokala – på samma sätt som du är bekant med SQL Server i den lokala världen.

När du skapar en SQL Database-server anger du ett serverinloggningskonto och lösenord som har administrativa rättigheter till huvuddatabasen på den servern och alla databaser som skapats på den servern. Det här första kontot är ett SQL-inloggningskonto. Azure SQL Database stöder SQL-autentisering och Azure Active Directory-autentisering för autentisering. Information om inloggningar och autentisering finns [i Hantera databaser och inloggningar i Azure SQL Database](sql-database-manage-logins.md). Windows-autentisering stöds inte.

En SQL-databasserver:

- Skapas i en Azure-prenumeration, men kan flyttas till en annan prenumeration tillsammans med de resurser som den innehåller.
- Är den överordnade resursen för databaser, elastiska pooler och informationslager.
- Tillhandahåller ett namnområde för databaser, elastiska pooler och informationslager
- Är en logisk behållare med stark livstidssemantik – ta bort en server och den tar bort inneslutna databaser, elastiska pooler och informationslager
- Deltar i [Azure-rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview) – databaser, elastiska pooler och informationslager på en server ärver åtkomsträttigheter från servern
- Är ett högordselement för identiteten för databaser, elastiska pooler och datalager för Azure-resurshantering (se URL-schemat för databaser och pooler)
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för databasåtkomst (`<serverName>`.database.windows.net)
- Ger åtkomst till metadata för inneslutna resurser via DMV:er genom att ansluta till en huvuddatabas.
- Innehåller utrymme för hanteringsprinciper som gäller för dess databaser - inloggningar, brandvägg, granskning, hotidentifiering och sådant
- Begränsas av en kvot inom den överordnade prenumerationen (sex servrar per prenumeration som standard - [se Prenumerationsgränser här)](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Anger omfånget för databaskvot och DTU- eller vCore-kvot för de resurser som den innehåller (till exempel 45 000 DTU)
- Är versionsomfånget för funktioner aktiverat för inneslutna resurser
- Huvudkontoinloggningar på servernivå kan hantera alla databaser på en server.
- Kan innehålla inloggningar som liknar de i instanser av SQL Server i din lokala miljö som har åtkomst till en eller flera databaser på servern, samt kan beviljas begränsade administrativa rättigheter. Mer information finns i avsnittet om [inloggningar](sql-database-manage-logins.md).
- Standardsorteringen för alla databaser som skapas `SQL_LATIN1_GENERAL_CP1_CI_AS`på `LATIN1_GENERAL` en SQL Database-server är , där är engelska (USA), `CP1` är teckentabell 1252, `CI` är skiftlägesokänslig och `AS` är accentkänslig.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Azure-portalen

Du kan skapa Azure SQL-databasens resursgrupp i förväg eller när du skapar själva servern. Det finns flera metoder för att komma till ett nytt SQL-serverformulär, antingen genom att skapa en ny SQL-server eller som en del av att skapa en ny databas.

### <a name="create-a-blank-sql-database-server"></a>Skapa en tom SQL Database-server

Om du vill skapa en Azure SQL Database-server (utan databas) med Hjälp av [Azure-portalen](https://portal.azure.com)navigerar du till ett tomt SQL-serverformulär (logisk server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Skapa en tom eller exempeld-SQL-databas

Om du vill skapa en Azure SQL-databas med [Azure-portalen](https://portal.azure.com)navigerar du till ett tomt SQL-databasformulär och tillhandahåller den begärda informationen. Du kan skapa Azure SQL-databasens resursgrupp och SQL Database-server i förväg eller när du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserad på Adventure Works LT.

  ![skapa databas-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för databasen finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).

Om du vill skapa en hanterad instans läser du [Skapa en hanterad instans](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Hantera en befintlig SQL-server

Om du vill hantera en befintlig server navigerar du till servern med hjälp av ett antal metoder , till exempel från specifik SQL-databassida, **sql-servrar** eller sidan **Alla resurser.**

Om du vill hantera en befintlig databas navigerar du till **sidan SQL-databaser** och klickar på den databas som du vill hantera. Följande skärmbild visar hur du börjar ställa in en brandvägg på servernivå för en databas från **sidan Översikt** för en databas.

   ![brandväggsregler för server](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Information om hur du konfigurerar prestandaegenskaper för en databas finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).
> [!TIP]
> En snabbstart för Azure-portalen finns [i Skapa en Azure SQL-databas i Azure-portalen](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Hantera Azure SQL-servrar, databaser och brandväggar med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill skapa och hantera Azure SQL-server, databaser och brandväggar med Azure PowerShell använder du följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell läser du [Installera Azure PowerShell-modul](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar elastiska pooler finns i [Elastiska pooler](sql-database-elastic-pool.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en databas |
|[Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Ta bort-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Skapar en resursgrupp|
|[Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Skapar en server|
|[Hämta AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Returnerar information om servrar|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Ändrar egenskaper för en server|
|[Ta bort-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Tar bort en server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Få-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Ta bort-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| Ny-AzSqlServerVirtualNetworkRule | Skapar en [*regel för virtuellt nätverk*](sql-database-vnet-service-endpoint-rule-overview.md), baserat på ett undernät som är en slutpunkt för tjänsten Virtuellt nätverk. |

> [!TIP]
> En PowerShell-snabbstart finns i [Skapa en enda Azure SQL-databas med PowerShell](sql-database-single-database-get-started.md). PowerShell-exempelskript finns i [Använda PowerShell för att skapa en enda Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-powershell.md) och [övervaka och skala en enda Azure SQL-databas med PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Hjälp av Azure CLI

Om du vill skapa och hantera Azure SQL-server, databaser och brandväggar med [Azure CLI](/cli/azure)använder du följande [Azure CLI SQL Database-kommandon.](/cli/azure/sql/db) Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Information om hur du skapar och hanterar elastiska pooler finns i [Elastiska pooler](sql-database-elastic-pool.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Skapar en databas|
|[az sql db lista](/cli/azure/sql/db#az-sql-db-list)|Visar alla databaser och informationslager på en server, eller alla databaser i en elastisk pool|
|[az sql db list-utgåvor](/cli/azure/sql/db#az-sql-db-list-editions)|Visar tillgängliga tjänstmål och lagringsgränser|
|[az sql db list-användningar](/cli/azure/sql/db#az-sql-db-list-usages)|Returnerar databasanvändningar|
|[az sql db visa](/cli/azure/sql/db#az-sql-db-show)|Hämtar en databas eller ett informationslager|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Uppdaterar en databas|
|[az sql db ta bort](/cli/azure/sql/db#az-sql-db-delete)|Tar bort en databas|
|[az group create](/cli/azure/group#az-group-create)|Skapar en resursgrupp|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Skapar en server|
|[az sql-serverlista](/cli/azure/sql/server#az-sql-server-list)|Visar servrar|
|[az sql server list-användningar](/cli/azure/sql/server#az-sql-server-list-usages)|Returnerar serveranvändning|
|[az sql server visa](/cli/azure/sql/server#az-sql-server-show)|Hämtar en server|
|[az sql server uppdatering](/cli/azure/sql/server#az-sql-server-update)|Uppdaterar en server|
|[az sql server ta bort](/cli/azure/sql/server#az-sql-server-delete)|Tar bort en server|
|[az sql server brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Skapar en serverbrandväggsregel|
|[az sql server brandvägg-regel lista](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Visar brandväggsreglerna på en server|
|[az sql server brandvägg-regel visa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Visar detaljerna i en brandväggsregel|
|[az sql server brandvägg-regel uppdatering](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Uppdaterar en brandväggsregel|
|[az sql server brandvägg-regel bort](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tar bort en brandväggsregel|

> [!TIP]
> En Azure CLI-snabbstart finns i [Skapa en enda Azure SQL-databas med Azure CLI](sql-database-cli-samples.md). Azure CLI-exempelskript finns i [Använda CLI för att skapa en enda Azure SQL-databas och konfigurera en brandväggsregel](scripts/sql-database-create-and-configure-database-cli.md) och Använda CLI för att övervaka och skala en enda Azure [SQL-databas](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Hantera Azure SQL-servrar, databaser och brandväggar med Transact-SQL

Om du vill skapa och hantera Azure SQL-server, databaser och brandväggar med Transact-SQL använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure-portalen, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. För hantering av elastiska pooler, se [Elastiska pooler](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med Transact-SQL.
>

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS (Azure SQL-databas)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas. Du måste vara ansluten till huvuddatabasen för att kunna skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Ändrar en Azure SQL-databas. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Ändrar ett Azure SQL Data Warehouse.|
|[DROP-DATABAS (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänstnivå), tjänstmål (prisnivå) och eventuellt elastiskt poolnamn för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Om du är inloggad på huvuddatabasen i en Azure SQL Database-server returnerar du information om alla databaser. För Azure SQL Data Warehouse måste du vara ansluten till huvuddatabasen.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU-, I/O- och minnesförbrukning för en Azure SQL Database-databas. En rad finns för var 15:e sekund, även om det inte finns någon aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagringsdata för en Azure SQL-databas. Uppgifterna samlas in och aggregeras inom fem minuters intervall.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för anslutningshändelser för SQL Database-databasen, vilket ger en översikt över misslyckade databaseranslutningar och fel. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade Azure SQL Database-databasanslutningar, anslutningsfel och dödlägen. Du kan använda den här informationen för att spåra eller felsöka databasaktiviteten med SQL Database.|
|[sp_set_firewall_rule (Azure SQL-databas)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsinställningarna på servernivå för SQL Database-servern. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudinloggningen på servernivå. En brandväggsregel på servernivå kan bara skapas med Transact-SQL efter att den första brandväggsregeln på servernivå har skapats av en användare med behörigheter på Azure-nivå|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på servernivå som är associerade med Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL-databas)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningar på servernivå från SQL Database-servern. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudinloggningen på servernivå.|
|[sp_set_database_firewall_rule (Azure SQL-databas)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsreglerna på databasnivå för din Azure SQL Database eller SQL Data Warehouse. Databasbrandväggsregler kan konfigureras för huvuddatabasen och för användardatabaser i SQL Database. Databasbrandväggsregler är användbara när du använder innehållna databasanvändare. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på databasnivå som är associerade med Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL-databas)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brandväggsinställning på databasnivå från din Azure SQL Database eller SQL Data Warehouse. |

> [!TIP]
> En snabbstart med SQL Server Management Studio i Microsoft Windows finns i [Azure SQL Database: Använd SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md). En snabbstart med Visual Studio-kod på macOS, Linux eller Windows finns i [Azure SQL Database: Använd Visual Studio-kod för att ansluta och fråga data](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Hantera Azure SQL-servrar, databaser och brandväggar med REST API

Om du vill skapa och hantera Azure SQL-server, databaser och brandväggar använder du dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar - Ta bort](https://docs.microsoft.com/rest/api/sql/servers/delete)|Tar bort en SQL-server.|
|[Servrar - Hämta](https://docs.microsoft.com/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Returnerar en lista över servrar.|
|[Servrar - Lista efter resursgrupp](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista över servrar i en resursgrupp.|
|[Servrar - Uppdatering](https://docs.microsoft.com/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Databaser - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser - Ta bort](https://docs.microsoft.com/rest/api/sql/databases/delete)|Tar bort en databas.|
|[Databaser - Hämta](https://docs.microsoft.com/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser - Lista efter elastisk pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - Lista efter server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser på en server.|
|[Databaser - Uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brandväggsregler - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brandväggsregel.|
|[Brandväggsregler - Ta bort](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Tar bort en brandväggsregel.|
|[Brandväggsregler - Hämta](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Hämtar en brandväggsregel.|
|[Brandväggsregler - Lista efter server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista över brandväggsregler.|

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du migrerar en SQL Server-databas till Azure finns i [Migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
