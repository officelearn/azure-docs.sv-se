---
title: PowerShell-exempel - Uppdatera synkroniseringsschema för SQL Data Sync
description: Azure PowerShell-exempelskript för uppdatering av synkroniseringsschemat för SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 00f9e217df65838e75f351dfe00fb60594acdcfc
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383500"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp

Det här PowerShell-exemplet uppdaterar synkroniseringsschemat i en befintlig synkroniseringsgrupp för SQL Data Sync. När du synkroniserar flera tabeller får du hjälp av det här skriptet att uppdatera synkroniseringsschemat på ett effektivt sätt. Det här exemplet visar hur skriptet **UpdateSyncSchema** används. Det finns på GitHub som [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync stöder inte Azure SQL-databashanterad instans just nu.

## <a name="examples"></a>Exempel

### <a name="add-all-tables-to-the-sync-schema"></a>Lägga till alla tabeller i synkroniseringsschemat

I följande exempel uppdateras databasschemat och alla giltiga tabeller läggs till i synkroniseringsschemat i hubbdatabasen.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Lägga till och ta bort tabeller och kolumner

I följande exempel läggs `[dbo].[Table1]` och `[dbo].[Table2].[Column1]` till i synkroniseringsschemas, och `[dbo].[Table3]` tas bort.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparametrar

Skriptet **UpdateSyncSchema** innehåller följande parametrar:

| Parameter | Anteckningar |
|---|---|
| $subscriptionId | Den prenumeration där synkroniseringsgruppen skapas. |
| $resourceGroupName | Den resursgrupp där synkroniseringsgruppen skapas.|
| $serverName | Hubbdatabasens servernamn.|
| $databaseName | Hubbdatabasens namn. |
| $syncGroupName | Synkroniseringsgruppens namn. |
| $memberName | Ange medlemsnamnet om du vill läsa in databasschemat från synkroniseringsmedlemmen i stället för från hubbdatabasen. Lämna den här parametern tom om du vill läsa in databasschemat från hubben. |
| $timeoutInSeconds | Tidsgräns när skriptet uppdaterar databasschemat. Standardvärdet är 900 sekunder. |
| $refreshDatabaseSchema | Ange om skriptet behöver uppdatera databasschemat. Om ditt databasschemat har ändrats från den tidigare konfigurationen, till exempel om du har lagt till en ny tabell eller kolumn, måste du uppdatera schemat innan du konfigurerar om det. Standardvärdet är false. |
| $addAllTables | Om värdet är true läggs alla giltiga tabeller och kolumner till i synkroniseringsschemat. Värdena för $TablesAndColumnsToAdd och $TablesAndColumnsToRemove ignoreras. |
| $tablesAndColumnsToAdd | Ange tabeller eller kolumner som ska läggas till i synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell- och kolumnnamn kan anges och avgränsas med kommatecken (,). |
| $tablesAndColumnsToRemove | Ange tabeller eller kolumner som ska tas bort från synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell- och kolumnnamn kan anges och avgränsas med kommatecken (,). |

## <a name="script-explanation"></a>Förklaring av skript

Skriptet **UpdateSyncSchema** använder följande parametrar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Returnerar information om en synkroniseringsgrupp. |
| [Uppdatering-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Uppdaterar en synkroniseringsgrupp. |
| [Hämta AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Returnerar information om en synkroniseringsmedlem. |
| [Hämta-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Returnerar information om ett synkroniseringsschema. |
| [Uppdatering-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Uppdaterar ett synkroniseringsschema. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare exempel på PowerShell-skript i SQL-databasen finns i [Azure SQL-databasen PowerShell-skript](../sql-database-powershell-samples.md).

Mer information om SQL Data Sync finns i:

- Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-database-sync-data.md)
- Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](../sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        - [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](sql-database-sync-data-between-sql-databases.md)
        - [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](sql-database-sync-data-between-azure-onprem.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](../sql-database-data-sync-agent.md)
- Metodtips – [Metodtips för Azure SQL Data Sync](../sql-database-best-practices-data-sync.md)
- Övervaka - [Övervaka SQL Data Sync med Azure Monitor-loggar](../sql-database-sync-monitor-oms.md)
- Felsökning – [Felsöka problem med Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md)
- Uppdatera synkroniseringsschemat
    - Med Transact-SQL – [Automatisera replikeringen av schemaändringar i Azure SQL Data Sync](../sql-database-update-sync-schema.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](../sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
