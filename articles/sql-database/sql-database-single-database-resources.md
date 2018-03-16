---
title: Azure SQL Database enskild databas | Microsoft Docs
description: "Hantera den tjänstnivå och prestandanivå mängden storagea för en enda Azure SQL-databas."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 167a72ae55052b8ac1dfe8f032f136a9bf8bcedf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Hantera resurser för en enskild databas i Azure SQL Database

Med en enskild databas bestämmer du mängden resurser som krävs för databasen för att hantera dess arbetsbelastningen på tjänstnivå, prestanda och mängden lagringsutrymme som krävs. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Hantera enskilda databaser med Azure-portalen

Om du vill ange eller ändra tjänstnivå, prestanda eller lagringsutrymmet för en ny eller befintlig Azure SQL-databas med hjälp av Azure portal, öppna den **konfigurera prestanda** fönster för databasen genom att klicka på **prisnivå (skala dtu: er)** – som visas i följande skärmbild. 

- Ange eller ändra tjänstnivå genom att välja tjänstnivån för din arbetsbelastning. 
- Ange eller ändra prestandanivå (**dtu: er**) inom en nivå med hjälp av **DTU** skjutreglaget.
- Ange eller ändra lagringsutrymmet för prestanda nivå med den **lagring** skjutreglaget. 

![Konfigurera tjänstnivå och prestandanivå servicenivå](./media/sql-database-single-database-resources/change-service-tier.png)

Klicka på **översikt** att övervaka och/eller avbryta en pågående åtgärd.

![Avbryt åtgärden](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Granska [aktuella begränsningar av P11 och P15 databaser med 4 TB maximal storlek](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) när du väljer en P11 eller P15 tjänstnivå.
>

## <a name="manage-single-database-resources-using-powershell"></a>Hantera enskilda databaser med PowerShell

Om du vill ange eller ändra Azure SQL-databaser Använd servicenivåer och prestandanivåer lagringsutrymme med hjälp av PowerShell, följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en databas |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas till en elastisk pool. Till exempel använda den **MaxSizeBytes** egenskapen anges den maximala storleken för en databas.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Hämtar status för databasåtgärder. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Avbryter asynkron update-åtgärden i databasen.|


> [!TIP]
> Ett exempel PowerShell-skript som övervakar prestandamått för en databas kan skalas till en högre prestandanivå och skapar en aviseringsregel på något av prestandamåtten, finns [Övervakare och skala en enskild SQL-databas med hjälp av PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Hantera enskilda databaser med Azure CLI

Att ange eller ändra Azure SQL-databaser servicenivåer och prestandanivåer lagringsutrymme med hjälp av Azure CLI använda dessa [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Skapa och hantera SQL elastiska pooler finns [elastiska pooler](sql-database-elastic-pool.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Skapar en brandväggsregel|
|[AZ sql server-brandväggsregel lista](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Visar en lista över brandväggsregler på en server|
|[AZ sql server-brandväggsregel visa](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Visar information om en brandväggsregel|
|[uppdatering av AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uppdaterar en brandväggsregel|
|[ta bort AZ sql server-brandväggsregel](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Tar bort en brandväggsregel|
|[AZ sql db op lista](/cli/azure/sql/db/op?#az_sql_db_op_list)|Hämtar en lista över åtgärder som utförs på databasen.|
|[AZ sql db op Avbryt](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Avbryter den asynkrona åtgärden i databasen.|

> [!TIP]
> Ett Azure CLI exempelskript som kan skalas en enskild Azure SQL-databas till en annan prestandanivå efter frågar informationen storleken på databasen, se [Använd CLI för att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Hantera enskild databas med Transact-SQL

Om du vill ange eller ändra Azure SQL-databaser servicenivåer och prestandanivåer lagringsutrymmet med Transact-SQL, kan du använda dessa T-SQL-kommandon. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. 

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Ändrar en Azure SQL database. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivån), tjänstmålet (prisnivån) och namn på elastisk pool, för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. För Azure SQL Data Warehouse, måste du vara ansluten till master-databasen.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Visar antalet, typen och varaktighet för databaser på en Azure SQL Database-server.|

I följande exempel visas den maximala storleken för en databas ändras med hjälp av ALTER DATABASE-kommandot:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Hantera enskilda databaser med REST API

Om du vill ange eller ändra Azure SQL-databaser servicenivåer och prestandanivåer lagringsutrymme kan du använda dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en recommented elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en recommented elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Operations - lista](/rest/api/sql/Operations/List)|Visar alla tillgängliga SQL Rest API-åtgärder.|



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om servicenivåer och prestandanivåer lagring belopp, se [tjänstnivåer](sql-database-service-tiers.md).
- Lär dig mer om elastiska pooler, se [elastiska pooler](sql-database-elastic-pool.md).
- Lär dig mer om [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
