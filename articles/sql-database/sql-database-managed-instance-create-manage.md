---
title: Api-referens för hantering för hanterad instans
description: Lär dig mer om hur du skapar och hanterar hanterade azure SQL-databasinstanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268865"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Hanterad API-referens för hanterade Azure SQL-databasinstanser

Du kan skapa och hantera hanterade Azure SQL-databasinstanser med Azure-portalen, PowerShell, Azure CLI, REST API och Transact-SQL. I den här artikeln kan du hitta en översikt över funktioner och API som du kan använda för att skapa och konfigurera hanterad instans.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-portal: Skapa en hanterad instans

En snabbstart som visar hur du skapar en hanterad Azure SQL-databas-instans finns i [Snabbstart: Skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Skapa och hantera hanterade instanser

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill skapa och hantera hanterade instanser med Azure PowerShell använder du följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell läser du [Installera Azure PowerShell-modul](/powershell/azure/install-az-ps).

> [!TIP]
> PowerShell-exempelskript finns i [Snabbstartsskript: Skapa Azure SQL-hanterad instans med PowerShell-bibliotek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Beskrivning |
| --- | --- |
|[Ny-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Skapar en hanterad Azure SQL-databas-instans |
|[Få-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Returnerar information om Azure SQL-hanterad instans|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Anger egenskaper för en hanterad Azure SQL-databas-instans|
|[Ta bort AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Tar bort en Azure SQL-hanterad databasinstans|
|[Nya AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Skapar en Azure SQL Database-databas för hanterad instans för SQL-databas|
|[Hämta AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Returnerar information om Azure SQL-databas för hanterad instans|
|[Ta bort-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Tar bort en Azure SQL-databas för hanterade databasinstanser|
|[Återställ-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Återställer en Azure SQL-databas för hanterade databasinstanser|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Skapa och hantera hanterade instanser

Om du vill skapa och hantera hanterade instanser med [Azure CLI](/cli/azure)använder du följande Azure [CLI SQL-kommandon för hanterade instanser.](/cli/azure/sql/mi) Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> En Azure CLI-snabbstart finns i [Arbeta med SQL-hanterad instans med Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql mi skapa](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Skapar en hanterad instans|
|[az sql mi lista](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Visar tillgängliga hanterade instanser|
|[az sql mi show az sql mi show az sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Hämta information för en hanterad instans|
|[az sql mi uppdatering](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Uppdaterar en hanterad instans|
|[az sql mi ta bort](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Tar bort en hanterad instans|
|[az sql midb skapa](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Skapar en hanterad databas|
|[az sql midb lista](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Visar tillgängliga hanterade databaser|
|[az sql midb återställa](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Återställa en hanterad databas|
|[az sql midb delete az sql midb delete az sql midb delete az sql](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Tar bort en hanterad databas|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Skapa och hantera instansdatabaser

Om du vill skapa och hantera instansdatabasen när den hanterade instansen har skapats använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med azure-portalen, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), Azure Data [Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio-kod](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon.

> [!TIP]
> Snabbstartar som visar att du måste konfigurera och ansluta till en hanterad instans med SQL Server Management Studio i Microsoft Windows finns i [Snabbstart: Konfigurera Azure VM för att ansluta till en Hanterad Azure SQL-databas-instans](sql-database-managed-instance-configure-vm.md) och [Snabbstart: Konfigurera en point-to-site-anslutning till en Azure SQL-databashanterad instans från lokala](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en hanterad instans med Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Skapar en ny databas för hanterad instans. Du måste vara ansluten till huvuddatabasen för att kunna skapa en ny databas.|
| [ÄNDRA DATABAS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändrar en Azure SQL-hanterad instansdatabas.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Skapa och hantera hanterade instanser

Om du vill skapa och hantera hanterade instanser använder du dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Skapar eller uppdaterar en hanterad instans.|
|[Hanterade instanser - Ta bort](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Tar bort en hanterad instans.|
|[Hanterade instanser - Hämta](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Hämtar en hanterad instans.|
|[Hanterade instanser - lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Returnerar en lista över hanterade instanser i en prenumeration.|
|[Hanterade instanser - lista efter resursgrupp](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Returnerar en lista över hanterade instanser i en resursgrupp.|
|[Hanterade instanser – uppdatering](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Uppdaterar en hanterad instans.|

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du migrerar en SQL Server-databas till Azure finns i [Migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).
