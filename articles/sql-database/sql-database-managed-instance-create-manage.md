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
ms.date: 01/25/2019
ms.openlocfilehash: 852f181ff0bb84bc3bcdb6d674ecf72eca900d7d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312587"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Hanterade API-referens för Azure SQL Database-hanterade instanser

Du kan skapa och hantera Azure SQL Database-hanterade instanser med hjälp av Azure portal, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln hittar du en översikt över funktioner och API: et som du kan använda för att skapa och konfigurera Managed Instance.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-portalen: Skapa en hanterad instans

En Snabbstart som visar hur du skapar en Azure SQL Database Managed Instance finns [Snabbstart: Skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Skapa och hantera hanterade instanser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd följande PowerShell-cmdletar för att skapa och hantera hanterade instanser med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell kan du läsa [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

> [!TIP]
> Exempelskript för PowerShell, se [Snabbstart skript: Skapa Azure SQL Managed Instance med hjälp av PowerShell-biblioteket](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beskrivning |
| --- | --- |
|[Ny AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Skapar en Azure SQL Database Managed Instance |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Returnerar information om Azure SQL Managed Instance|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Anger egenskaperna för en Azure SQL Database Managed Instance|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Tar bort en hanterad Azure SQL-databasinstans|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Skapar en Azure SQL Database Managed Instance-databas|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Returnerar information om Azure SQL Managed Instance-databasen|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Tar bort en hanterad databasinstans i Azure SQL-databas|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Återställer en hanterad databasinstans i Azure SQL-databas|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Skapa och hantera hanterade instanser

Skapa och hantera hanterade instanser med [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> En Snabbstart för Azure CLI, se [arbetar med SQL Managed Instance med Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Skapar en hanterad instans|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Visar tillgängliga hanterade instanser|
|[AZ sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Hämta information för en hanterad instans|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Uppdaterar en hanterad instans|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Tar bort en hanterad instans|
|[Skapa AZ sql EXTEXTB](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Skapar en hanterad databas|
|[AZ sql EXTEXTB lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Listor över hanterade databaser|
|[AZ sql EXTEXTB återställning](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Återställa en hanterad databas|
|[AZ sql EXTEXTB delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Tar bort en hanterad databas|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Skapa och hantera instansdatabaser

Använd följande T-SQL-kommandon för att skapa och hantera databasinstans när den hanterade instansen har skapats. Du kan skicka dessa kommandon med hjälp av Azure-portalen [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon.

> [!TIP]
> Snabbstart som visar du behöva konfigurera och ansluta till en hanterad instans med SQL Server Management Studio på Microsoft Windows finns i [snabbstarten: Konfigurera en virtuell Azure-dator för att ansluta till en hanterad Azure SQL Database-instans](sql-database-managed-instance-configure-vm.md) och [Snabbstart: Konfigurera en punkt-till-plats-anslutning till en hanterad Azure SQL Database-instans lokalt](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Skapar en ny hanterad instans-databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändrar en databas i Azure SQL Managed Instance.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST-API: Skapa och hantera hanterade instanser

Använd dessa REST API-begäranden för att skapa och hantera instanser som hanteras.

| Kommando | Beskrivning |
| --- | --- |
|[Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Skapar eller uppdaterar en hanterad instans.|
|[Hanterade instanser - ta bort](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Tar bort en hanterad instans.|
|[Hanterade instanser - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Hämtar en hanterad instans.|
|[Hanterade instanser - lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Returnerar en lista över hanterade instanser i en prenumeration.|
|[Hanterade instanser - lista efter resursgrupp](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Returnerar en lista över hanterade instanser i en resursgrupp.|
|[Hanterade instanser - uppdatering](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Uppdaterar en hanterad instans.|

## <a name="next-steps"></a>Nästa steg

- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
