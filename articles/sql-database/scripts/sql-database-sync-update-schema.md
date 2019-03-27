---
title: Exempel på PowerShell – Uppdatera synkroniseringsschema för SQL Data Sync | Microsoft Docs
description: Azure PowerShell-exempelskript för uppdatering av synkroniseringsschemat för SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 96f5cf6bb8ad6cd87b933f08add6e25c4f4ec766
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485195"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp

Det här PowerShell-exemplet uppdaterar synkroniseringsschemat i en befintlig synkroniseringsgrupp för SQL Data Sync. När du synkroniserar flera tabeller får du hjälp av det här skriptet att uppdatera synkroniseringsschemat på ett effektivt sätt. Det här exemplet visar hur skriptet **UpdateSyncSchema** används. Det finns på GitHub som [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync har **inte** stöd för Azure SQL Database Managed Instance just nu.

## <a name="sample-script"></a>Exempelskript

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exempel 1 – Lägga till alla tabeller till synkroniseringsschemat

I följande exempel uppdateras databasschemat och alla giltiga tabeller läggs till i synkroniseringsschemat i hubbdatabasen.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exempel 2 – Lägga till och ta bort tabeller och kolumner

I följande exempel läggs `[dbo].[Table1]` och `[dbo].[Table2].[Column1]` till i synkroniseringsschemas, och `[dbo].[Table3]` tas bort.

```powershell-interactive
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
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Returnerar information om en synkroniseringsgrupp. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Uppdaterar en synkroniseringsgrupp. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Returnerar information om en synkroniseringsmedlem. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Returnerar information om ett synkroniseringsschema. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Uppdaterar ett synkroniseringsschema. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).

Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server lokalt](../sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](../sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
-   Övervaka – [övervaka SQL Data Sync med Azure Monitor-loggar](../sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](../sql-database-update-sync-schema.md)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](../sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
