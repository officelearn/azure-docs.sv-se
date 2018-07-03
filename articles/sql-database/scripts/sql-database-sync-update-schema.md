---
title: Exempel på PowerShell – Uppdatera synkroniseringsschema för SQL Data Sync | Microsoft Docs
description: Azure PowerShell-exempelskript för uppdatering av synkroniseringsschemat för SQL Data Sync
services: sql-database
documentationcenter: sql-database
author: allenwux
manager: craigg
editor: ''
tags: ''
ms.assetid: ''
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: ee491ab02305b858241ba5ddc2ec7651b7802be0
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37024809"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp

Det här PowerShell-exemplet uppdaterar synkroniseringsschemat i en befintlig synkroniseringsgrupp för SQL Data Sync. När du synkroniserar flera tabeller får du hjälp av det här skriptet att uppdatera synkroniseringsschemat på ett effektivt sätt.

Det här exemplet visar hur skriptet **UpdateSyncSchema** används. Det finns på GitHub som [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Nödvändiga komponenter

Det här exemplet kräver Azure PowerShell-modul version 4.2 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).
 
Kör `Connect-AzureRmAccount` för att skapa en anslutning med Azure.

## <a name="examples"></a>Exempel

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exempel 1 – Lägga till alla tabeller till synkroniseringsschemat

I följande exempel uppdateras databasschemat och alla giltiga tabeller läggs till i synkroniseringsschemat i hubbdatabasen.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exempel 2 – Lägga till och ta bort tabeller och kolumner

I följande exempel läggs `[dbo].[Table1]` och `[dbo].[Table2].[Column1]` till i synkroniseringsschemas, och `[dbo].[Table3]` tas bort.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparametrar

Skriptet **UpdateSyncSchema** innehåller följande parametrar:

| Parameter | Anteckningar |
|---|---|
| $SubscriptionId | Den prenumeration där synkroniseringsgruppen skapas. |
| $ResourceGroupName | Den resursgrupp där synkroniseringsgruppen skapas.|
| $ServerName | Hubbdatabasens servernamn.|
| $DatabaseName | Hubbdatabasens namn. |
| $SyncGroupName | Synkroniseringsgruppens namn. |
| $MemberName | Ange medlemsnamnet om du vill läsa in databasschemat från synkroniseringsmedlemmen i stället för från hubbdatabasen. Lämna den här parametern tom om du vill läsa in databasschemat från hubben. |
| $TimeoutInSeconds | Tidsgräns när skriptet uppdaterar databasschemat. Standardvärdet är 900 sekunder. |
| $RefreshDatabaseSchema | Ange om skriptet behöver uppdatera databasschemat. Om ditt databasschemat har ändrats från den tidigare konfigurationen, till exempel om du har lagt till en ny tabell eller kolumn, måste du uppdatera schemat innan du konfigurerar om det. Standardvärdet är false. |
| $AddAllTables | Om värdet är true läggs alla giltiga tabeller och kolumner till i synkroniseringsschemat. Värdena för $TablesAndColumnsToAdd och $TablesAndColumnsToRemove ignoreras. |
| $TablesAndColumnsToAdd | Ange tabeller eller kolumner som ska läggas till i synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell- och kolumnnamn kan anges och avgränsas med kommatecken (,). |
| $TablesAndColumnsToRemove | Ange tabeller eller kolumner som ska tas bort från synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell- och kolumnnamn kan anges och avgränsas med kommatecken (,). |
|||

## <a name="script-explanation"></a>Förklaring av skript

Skriptet **UpdateSyncSchema** använder följande parametrar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Returnerar information om en synkroniseringsgrupp. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Uppdaterar en synkroniseringsgrupp. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Returnerar information om en synkroniseringsmedlem. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Returnerar information om ett synkroniseringsschema. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Uppdaterar ett synkroniseringsschema. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).

Mer information om SQL Data Sync finns i:

-   [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md)
-   [Konfigurera Azure SQL Data Sync](../sql-database-get-started-sql-data-sync.md)
-   [Metodtips för Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
-   [Övervaka Azure SQL Data Sync med Log Analytics](../sql-database-sync-monitor-oms.md)
-   [Felsöka problem med Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL Data Sync:
    -   [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](sql-database-sync-data-between-azure-onprem.md)

-   [Ladda ned REST API-dokumentation för SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](../sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
