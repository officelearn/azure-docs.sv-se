---
title: Hantera elastiska pooler
description: Skapa och hantera Azure SQL elastiska pooler.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: d8dde76753e58c713763c16230e5461fef43be88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067338"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Hantera elastiska pooler i Azure SQL Database

Med en elastisk pool bestämmer du hur mycket resurser som den elastiska poolen behöver för att hantera arbetsbelastningen för sina databaser och mängden resurser för varje poolad databas.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure-portal: Hantera elastiska pooler och poolade databaser

Alla poolinställningar finns på ett ställe: **Konfigurera poolbladet.** För att komma hit, hitta en elastisk pool i portalen och klicka på **Konfigurera pool** antingen från toppen av bladet eller från resursmenyn till vänster.

Härifrån kan du göra en kombination av följande ändringar och spara dem alla i en batch:

1. Ändra poolens tjänstnivå
2. Skala prestanda (DTU eller vCores) och lagring upp eller ned
3. Lägga till eller ta bort databaser i/från poolen
4. Ange en min (garanterad) och max prestandagräns för databaserna i poolerna
5. Granska kostnadssammanfattningen om du vill visa eventuella ändringar i fakturan som ett resultat av dina nya val

![Elastiskt poolkonfigurationsblad](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Hantera elastiska pooler och poolade databaser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill skapa och hantera elastiska SQL Database-pooler och poolade databaser med Azure PowerShell använder du följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell läser du [Installera Azure PowerShell-modul](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar SQL Database-servrarna för en elastisk pool finns i [Skapa och hantera SQL Database-servrar](sql-database-servers.md). Om du vill skapa och hantera brandväggsregler finns i [Skapa och hantera brandväggsregler med PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> PowerShell-exempelskript finns i [Skapa elastiska pooler och flytta databaser mellan pooler och ut ur en pool med PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en ELASTISK SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Ny-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Skapar en elastisk pool.|
|[Få-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hämtar elastiska pooler och deras egenskapsvärden.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändrar egenskaper för en elastisk pool Använd till exempel egenskapen **StorageMB** för att ändra maxlagringen av en elastisk pool.|
|[Ta bort-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Tar bort en elastisk pool.|
|[Få-AzSqlElasticPoolAktivitet](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool|
|[Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en ny databas i en befintlig pool eller som en enda databas. |
|[Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till, ut ur eller mellan elastiska pooler.|
|[Ta bort-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas.|

> [!TIP]
> Det kan ta tid att skapa många databaser i en elastisk pool när du gör det med portalen eller PowerShell-cmdlets som bara skapar en enda databas åt gången. Om du vill automatisera skapandet till en elastisk pool finns i [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Hantera elastiska pooler och poolade databaser

Om du vill skapa och hantera elastiska SQL Database-pooler med [Azure CLI](/cli/azure)använder du följande Azure [CLI SQL-databaskommandon.](/cli/azure/sql/db) Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Azure CLI-exempelskript finns i [Använda CLI för att flytta en Azure SQL-databas i en ELASTISK SQL-pool](scripts/sql-database-move-database-between-pools-cli.md) och Använda Azure CLI för att skala en [SQL-elastisk pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql elastisk-pool skapa](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Skapar en elastisk pool.|
|[az sql elastisk-pool lista](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Returnerar en lista över elastiska pooler på en server.|
|[az sql elastisk-pool lista-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[az sql elastisk-pool list-utgåvor](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Innehåller även tillgängliga DTU-inställningar för pool, lagringsgränser och per databasinställningar. För att minska verbositeten döljs ytterligare lagringsgränser och per databasinställningar som standard.|
|[az sql elastisk-pool uppdatering](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Uppdaterar en elastisk pool.|
|[az sql elastisk-pool ta bort](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Hantera poolade databaser

Om du vill skapa och flytta databaser i befintliga elastiska pooler eller returnera information om en elastisk SQL-databaspool med Transact-SQL använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure-portalen, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. Om du vill skapa och hantera brandväggsregler med T-SQL finns i [Hantera brandväggsregler med Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en elastisk Azure SQL Database-pool med Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda DMV:er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS (Azure SQL-databas)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig pool eller som en enda databas. Du måste vara ansluten till huvuddatabasen för att kunna skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas till, ut ur eller mellan elastiska pooler.|
|[DROP-DATABAS (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resursanvändningsstatistik för alla elastiska pooler i en SQL Database-server. För varje elastisk pool finns det en rad för varje 15 sekunders rapporteringsfönster (fyra rader per minut). Detta inkluderar CPU, IO, Log, lagringsförbrukning och samtidig begäran /session användning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänstnivå), tjänstmål (prisnivå) och eventuellt elastiskt poolnamn för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Om du är inloggad på huvuddatabasen i en Azure SQL Database-server returnerar du information om alla databaser. För Azure SQL Data Warehouse måste du vara ansluten till huvuddatabasen.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Hantera elastiska pooler och poolade databaser

Om du vill skapa och hantera elastiska SQL Database-pooler och poolade databaser använder du dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler - Ta bort](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler - Få](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Får en elastisk pool.|
|[Elastiska pooler - Lista efter server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista över elastiska pooler på en server.|
|[Elastiska pooler - Uppdatering](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Uppdaterar en befintlig elastisk pool.|
|[Elastiska poolaktiviteter](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Returnerar elastiska poolaktiviteter.|
|[Aktiviteter för elastisk pooldatabas](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser inuti en elastisk pool.|
|[Databaser - Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser - Hämta](https://docs.microsoft.com/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser - Lista efter elastisk pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - Lista efter server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser på en server.|
|[Databaser - Uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](sql-database-wtp-overview.md).
