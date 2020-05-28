---
title: Hanterings-API-referens för hanterad instans
description: Lär dig mer om att skapa och hantera Azure SQL-hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: ca7e123a3a0a1707eb979f25cabfc5913c5bfcb8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046322"
---
# <a name="managed-api-reference-for-azure-sql-managed-instances"></a>Hanterad API-referens för Azure SQL Managed instances
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Du kan skapa och hantera Azure SQL-hanterade instanser med hjälp av Azure Portal, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln finns en översikt över funktioner och API som du kan använda för att skapa och konfigurera en hanterad instans.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: skapa en hanterad instans

En snabb start som visar hur du skapar en SQL Database Hanterad instans finns i [snabb start: skapa en SQL-hanterad instans](instance-create-quickstart.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: skapa och hantera hanterade instanser

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill skapa och hantera hanterade instanser med Azure PowerShell använder du följande PowerShell-cmdletar. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps).

> [!TIP]
> Skript för PowerShell-exempel finns i [snabb starts skript: skapa SQL-hanterad instans med PowerShell-bibliotek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Description |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Skapar en hanterad Azure SQL-instans |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Returnerar information om den hanterade Azure SQL-instansen|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Anger egenskaper för en hanterad Azure SQL-instans|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Tar bort en Azure SQL-hanterad databas instans|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Skapar en Azure SQL-hanterad instans databas|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Returnerar information om Azure SQL-hanterade instans databas|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Tar bort en Azure SQL-hanterad databas instans databas|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Återställer en Azure SQL-hanterad databas instans databas|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: skapa och hantera hanterade instanser

Om du vill skapa och hantera hanterade instanser med [Azure CLI](/cli/azure)använder du följande kommandon i [Azure CLI SQL-hanterade](/cli/azure/sql/mi) instanser. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> En snabb start för Azure CLI finns i [arbeta med SQL-hanterad instans med hjälp av Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Description |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Skapar en hanterad instans|
|[AZ SQL mi-lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Visar en lista över tillgängliga hanterade instanser|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Hämta information om en hanterad instans|
|[AZ SQL mi-uppdatering](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Uppdaterar en hanterad instans|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Tar bort en hanterad instans|
|[AZ SQL EXTEXTB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Skapar en hanterad databas|
|[AZ SQL EXTEXTB-lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Visar en lista över tillgängliga hanterade databaser|
|[AZ SQL EXTEXTB-återställning](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Återställa en hanterad databas|
|[ta bort AZ SQL EXTEXTB](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Tar bort en hanterad databas|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: skapa och hantera instans databaser

Använd följande T-SQL-kommandon för att skapa och hantera instans databasen när den hanterade instansen har skapats. Du kan utfärda dessa kommandon med hjälp av Azure Portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en server och skicka Transact-SQL-kommandon.

> [!TIP]
> För snabb starter som visar att du måste konfigurera och ansluta till en SQL-hanterad instans med SQL Server Management Studio på Microsoft Windows, se [snabb start: Konfigurera virtuell Azure-dator för att ansluta till en Azure SQL-hanterad instans](connect-vm-instance-configure.md) och [snabb start: Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans från den lokala](point-to-site-p2s-configure.md)datorn.
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med hjälp av Transact-SQL.

| Kommando | Description |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Skapar en ny hanterad instans databas. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändrar en Azure SQL-hanterad instans databas.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: skapa och hantera hanterade instanser

Använd dessa REST API begär Anden för att skapa och hantera hanterade instanser.

| Kommando | Description |
| --- | --- |
|[Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Skapar eller uppdaterar en hanterad instans.|
|[Hanterade instanser – ta bort](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Tar bort en hanterad instans.|
|[Hanterade instanser – Hämta](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Hämtar en hanterad instans.|
|[Hanterade instanser – lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Returnerar en lista med hanterade instanser i en prenumeration.|
|[Hanterade instanser – lista efter resurs grupp](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Returnerar en lista med hanterade instanser i en resurs grupp.|
|[Hanterade instanser – uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Uppdaterar en hanterad instans.|

## <a name="next-steps"></a>Nästa steg

- Information om hur du migrerar en SQL Server-databas till Azure finns i [migrera till Azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](../database/features-comparison.md).
