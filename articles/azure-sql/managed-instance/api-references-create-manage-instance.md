---
title: Hanterings-API-referens för Azure SQL-hanterad instans
description: Lär dig mer om att skapa och konfigurera hanterade instanser av Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 8cc2930422bf644f217737d0f0ba585c243575ee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503012"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Hanterad API-referens för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Du kan skapa och konfigurera hanterade instanser av Azure SQL-hanterad instans med hjälp av Azure Portal, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln finns en översikt över de funktioner och API: er som du kan använda för att skapa och konfigurera hanterade instanser.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: skapa en hanterad instans

En snabb start som visar hur du skapar en hanterad instans finns i [snabb start: skapa en hanterad instans](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: skapa och konfigurera hanterade instanser

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRM-modulerna är i stort sett identiska.

Om du vill skapa och hantera hanterade instanser med Azure PowerShell använder du följande PowerShell-cmdletar. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

> [!TIP]
> Exempel skript för PowerShell finns i [snabb starts skript: skapa en hanterad instans med ett PowerShell-bibliotek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Skapar en hanterad instans. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Returnerar information om en hanterad instans.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Anger egenskaper för en hanterad instans.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Tar bort en hanterad instans.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Skapar en SQL-hanterad instans databas.|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Returnerar information om en SQL-hanterad instans databas.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Tar bort en SQL-hanterad instans databas.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Återställer en SQL-hanterad instans databas.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: skapa och konfigurera hanterade instanser

Om du vill skapa och konfigurera hanterade instanser med [Azure CLI](/cli/azure)använder du följande [Azure CLI-kommandon för SQL-hanterad instans](/cli/azure/sql/mi). Använd [Azure Cloud Shell](/azure/cloud-shell/overview) för att köra cli i webbläsaren eller [Installera](/cli/azure/install-azure-cli) det på MacOS, Linux eller Windows.

> [!TIP]
> En snabb start för Azure CLI finns i [arbeta med SQL-hanterad instans med hjälp av Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Beskrivning |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Skapar en hanterad instans.|
|[AZ SQL mi-lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Visar en lista över tillgängliga hanterade instanser.|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Hämtar information om en hanterad instans.|
|[AZ SQL mi-uppdatering](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Uppdaterar en hanterad instans.|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Tar bort en hanterad instans.|
|[AZ SQL EXTEXTB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Skapar en hanterad databas.|
|[AZ SQL EXTEXTB-lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Visar en lista över tillgängliga hanterade databaser.|
|[AZ SQL EXTEXTB-återställning](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Återställer en hanterad databas.|
|[ta bort AZ SQL EXTEXTB](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Tar bort en hanterad databas.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: skapa och konfigurera instans databaser

Använd följande T-SQL-kommandon om du vill skapa och konfigurera instans databaser efter att den hanterade instansen har skapats. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en server och skicka Transact-SQL-kommandon.

> [!TIP]
> För snabb starter som visar hur du konfigurerar och ansluter till en hanterad instans med SQL Server Management Studio på Microsoft Windows, se [snabb start: Konfigurera virtuell Azure-dator för att ansluta till Azure SQL-hanterad instans](connect-vm-instance-configure.md) och [snabb start: Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans från den lokala](point-to-site-p2s-configure.md)datorn.

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Skapar en ny instans databas i SQL-hanterad instans. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändrar en instans databas i SQL-hanterad instans.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: skapa och konfigurera hanterade instanser

Använd dessa REST API begär Anden för att skapa och konfigurera hanterade instanser.

| Kommando | Beskrivning |
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
