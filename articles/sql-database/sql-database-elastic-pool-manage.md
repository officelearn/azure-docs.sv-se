---
title: Hantera elastiska pooler
description: Skapa och hantera elastiska Azure SQL-pooler.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067338"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Hantera elastiska pooler i Azure SQL Database

Med en elastisk pool fastställer du mängden resurser som den elastiska poolen kräver för att hantera arbets belastningen för databaserna och mängden resurser för varje databas i poolen.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: hantera elastiska pooler och databaser i pooler

Du hittar alla poolinställningarna på samma plats: bladet **Konfigurera pool** . Hämta en elastisk pool i portalen och klicka på **Konfigurera pool** , antingen längst upp på bladet eller på resurs menyn till vänster.

Härifrån kan du göra en kombination av följande ändringar och spara alla i en batch:

1. Ändra tjänst nivån för poolen
2. Skala prestandan (DTU eller virtuella kärnor) och lagring upp eller ned
3. Lägga till eller ta bort databaser i/från poolen
4. Ange lägsta (garanterade) och högsta prestanda gräns för databaserna i pooler
5. Granska kostnads översikten om du vill visa alla ändringar i fakturan som ett resultat av dina nya val

![Bladet konfiguration av Elastic pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: hantera elastiska pooler och databaser i pooler

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Använd följande PowerShell-cmdletar för att skapa och hantera SQL Database elastiska pooler och databaser i pooler med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar SQL Database-servrar för en elastisk pool finns i [skapa och hantera SQL Database-servrar](sql-database-servers.md). Information om att skapa och hantera brand Väggs regler finns i [skapa och hantera brand Väggs regler med hjälp av PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> För PowerShell-exempel skript, se [Skapa elastiska pooler och flytta databaser mellan pooler och från en pool med hjälp av PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Skapar en elastisk pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hämtar elastiska pooler och deras egenskaps värden.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändrar egenskaper för en elastisk pool Använd exempelvis egenskapen **StorageMB** för att ändra den maximala lagringen för en elastisk pool.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Tar bort en elastisk pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hämtar status för åtgärder i en elastisk pool|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en ny databas i en befintlig pool eller som en enskild databas. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till, från eller mellan elastiska pooler.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas.|

> [!TIP]
> Det kan ta tid att skapa många databaser i en elastisk pool när du gör det med hjälp av portalen eller PowerShell-cmdletar som bara skapar en databas åt gången. Om du vill skapa en elastisk pool automatiskt, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: hantera elastiska pooler och databaser i pooler

Använd följande [Azure cli SQL Database](/cli/azure/sql/db) -kommandon för att skapa och hantera SQL Database elastiska pooler med [Azure CLI](/cli/azure). Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Exempel skript för Azure CLI finns i [använda CLI för att flytta en Azure SQL-databas i en elastisk SQL-pool](scripts/sql-database-move-database-between-pools-cli.md) och [använda Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[AZ SQL Elastic-pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Skapar en elastisk pool.|
|[AZ SQL Elastic-pool List](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Returnerar en lista med elastiska pooler på en server.|
|[AZ SQL Elastic-pool List-databaser](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Returnerar en lista med databaser i en elastisk pool.|
|[AZ SQL Elastic-pool List-Editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Inkluderar också tillgängliga DTU-inställningar, lagrings gränser och inställningar per databas. För att minska utförligheten är ytterligare lagrings gränser och per databas inställningar dolda som standard.|
|[AZ SQL Elastic-pool Update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Uppdaterar en elastisk pool.|
|[AZ SQL Elastic-pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: hantera databaser i pooler

Om du vill skapa och flytta databaser inom befintliga elastiska pooler eller returnera information om en SQL Database elastisk pool med Transact-SQL, använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en Azure SQL Database Server och skicka Transact-SQL-kommandon. För att skapa och hantera brand Väggs regler med hjälp av T-SQL, se [Hantera brand Väggs regler med hjälp av Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database elastisk pool med hjälp av Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig pool eller som en enskild databas. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas till, ut eller mellan elastiska pooler.|
|[SLÄPP databas (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys. elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resursanvändnings statistik för alla elastiska pooler i en SQL Database-Server. För varje elastisk pool finns det en rad för varje 15 sekunders rapporterings fönster (fyra rader per minut). Detta omfattar CPU, IO, logg, lagrings användning och samtidig begäran/användning av alla databaser i poolen.|
|[sys. database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänst nivå), tjänst mål (pris nivå) och namn på elastisk pool, om det finns någon, för en Azure SQL-databas eller en Azure SQL Data Warehouse. Om du är inloggad på huvud databasen på en Azure SQL Database-Server returneras information om alla databaser. För Azure SQL Data Warehouse måste du vara ansluten till huvud databasen.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: hantera elastiska pooler och databaser i pooler

Använd dessa REST API förfrågningar för att skapa och hantera SQL Database elastiska pooler och databaser i pooler.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler – ta bort](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler – Hämta](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler – lista efter server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista med elastiska pooler på en server.|
|[Elastiska pooler – uppdatera](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Uppdaterar en befintlig elastisk pool.|
|[Aktiviteter för elastisk pool](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Returnerar aktiviteter för elastisk pool.|
|[Databas aktiviteter för elastisk pool](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet för databaser inuti en elastisk pool.|
|[Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Hämta](https://docs.microsoft.com/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista med elastisk pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista med databaser i en elastisk pool.|
|[Databaser – lista efter server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Returnerar en lista med databaser på en server.|
|[Databaser – uppdatera](https://docs.microsoft.com/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](sql-database-wtp-overview.md).
