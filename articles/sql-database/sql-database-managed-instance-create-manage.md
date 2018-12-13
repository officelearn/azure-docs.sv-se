---
title: Hanterings-API-referens för Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig mer om att skapa och hantera Azure SQL Database-hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7fb6917e129c015536143a707fd2a89fc5423a99
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323088"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Hanterade API-referens för Azure SQL Database-hanterade instanser

Du kan skapa och hantera Azure SQL Database-hanterade instanser med hjälp av Azure portal, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln hittar du en översikt över funktioner och API: et som du kan använda för att skapa och konfigurera Managed Instance.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-portalen: Skapa en hanterad instans

En Snabbstart som visar hur du skapar en Azure SQL Database Managed Instance finns [Snabbstart: Skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell: Skapa och hantera en hanterad instans

Använd följande PowerShell-cmdletar för att skapa och hantera Azure SQL server, databaser och brandväggar med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell kan du läsa [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps).

> [!TIP]
> Exempelskript för PowerShell, se [Snabbstart skript: Skapa Azure SQL Managed Instance med hjälp av PowerShell-biblioteket](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beskrivning |
| --- | --- |
|[Ny AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Skapar en Azure SQL Database Managed Instance |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Get-AzureRmSqlInstance)|Returnerar information om Azure SQL Managed Instance|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlInstance)|Anger egenskaperna för en Azure SQL Database Managed Instance|
|[Ta bort AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlInstance)|Tar bort en hanterad Azure SQL-databasinstans|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: Hantera logiska servrar och databaser

Skapa och hantera Azure SQL server, databaser och brandväggar med [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> En Snabbstart för Azure CLI, se [arbetar med SQL Managed Instance med Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql mi](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-create) |Skapar en hanterad instans|
|[AZ sql mi lista](https://docs.microsoft.com/cli/azure/sql/db#az-sql-mi-list)|Visar tillgängliga hanterade instanser|
|[AZ sql mi show](/cli/azure/sql/db#az-sql-mi-show)|Hämta information för en hanterad instans|
|[uppdatering av AZ sql mi](/cli/azure/sql/db#az-sql-mi-update)|Uppdaterar en hanterad instans|
|[AZ sql mi delete](/cli/azure/sql/db#az-sql-mi-delete)|Tar bort en hanterad instans|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: Hantera logiska servrar och databaser

Använd följande T-SQL-kommandon för att skapa och hantera Azure SQL Database Managed Instance databas när den hanterade instansen har skapats. Du kan skicka dessa kommandon med hjälp av Azure-portalen [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon.

> [!TIP]
> Snabbstart som visar du behöva konfigurera och ansluta till en hanterad instans med SQL Server Management Studio på Microsoft Windows finns i [snabbstarten: Konfigurera virtuell Azure-dator att ansluta till en Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) och [snabbstarten: Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Managed Instance från den lokala](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Skapar en ny hanterad instans-databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändrar en databas i Azure SQL Managed Instance.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST-API: Hantera logiska servrar och databaser

Använd dessa REST API-begäranden för att skapa och hantera Azure SQL Database Managed Instance.

| Kommando | Beskrivning |
| --- | --- |
|[Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Skapar eller uppdaterar en hanterad instans.|
|[Hanterade instanser - ta bort](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Tar bort en hanterad instans.|
|[Hanterade instanser - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Hämtar en hanterad instans.|
|[Hanterade instanser - lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Returnerar en lista över hanterade instanser i en prenumeration.|
|[Hanterade instanser - lista efter resursgrupp](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Returnerar en lista över hanterade instanser i en resursgrupp.|
|[Hanterade instanser - uppdatering](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Uppdaterar en hanterad instans.|

## <a name="next-steps"></a>Nästa steg

- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-cloud-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
