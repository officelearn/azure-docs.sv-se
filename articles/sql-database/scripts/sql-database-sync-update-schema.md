---
title: "Exempel på PowerShell - uppdatering SQL datasynkronisering synkroniseringsschema | Microsoft Docs"
description: "Azure PowerShell-exempelskript att uppdatera schemat synkronisering för SQL-datasynkronisering"
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 66bf084f585b86979e6521321daf466c571de10c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Använd PowerShell för att uppdatera schemat för synkronisering i en befintlig grupp för synkronisering

Det här exemplet PowerShell uppdaterar synkroniseringsschema i en befintlig grupp för synkronisering. När du synkronisera flera tabeller, hjälper dig att uppdatera schemat synkronisera effektivt i det här skriptet.

Det här exemplet visar användningen av den **UpdateSyncSchema** skript som finns på GitHub som [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

En översikt över SQL datasynkronisering finns [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Förutsättningar

Det här exemplet kräver Azure PowerShell Modulversion 4.2 eller senare. Kör `Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).
 
Kör `Login-AzureRmAccount` att skapa en anslutning med Azure.

## <a name="examples"></a>Exempel

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exempel 1 – lägga till alla tabeller i sync-schema

I följande exempel uppdaterar databasschemat och lägger till alla giltiga tabeller i databasen hubb sync-schemat.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exempel 2 - Lägg till och ta bort tabeller och kolumner

I följande exempel läggs `[dbo].[Table1]` och `[dbo].[Table2].[Column1]` synkroniseringsschema och tar bort `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparametrar

Den **UpdateSyncSchema** skriptet har följande parametrar:

| Parameter | Anteckningar |
|---|---|
| $SubscriptionId | Den prenumeration där sync-gruppen har skapats. |
| $ResourceGroupName | Resursgruppen där sync-gruppen har skapats.|
| $ServerName | Servernamnet för NAV-databasen.|
| $DatabaseName | Hubb-databasnamn. |
| $SyncGroupName | Namnet på synkronisering. |
| $MemberName | Ange Medlemsnamnet om du vill läsa in databasschemat från sync-medlemmen i stället för från NAV-databasen. Lämna den här parametern tomt om du vill läsa in databasschemat från hubben. |
| $TimeoutInSeconds | Timeout uppstod när skriptet uppdaterar databasschemat. Standardvärdet är 900 sekunder. |
| $RefreshDatabaseSchema | Ange om skriptet måste uppdatera databasschemat. Om din databasschemat har ändrats från den tidigare konfigurationen - till exempel om du har lagt till en ny tabell eller på nytt kolumn), behöver du uppdatera schemat innan du konfigurerar om. Standardvärdet är false. |
| $AddAllTables | Om värdet är true läggs alla giltiga tabeller och kolumner till sync-schemat. Värdena för $TablesAndColumnsToAdd och $TablesAndColumnsToRemove ignoreras. |
| $TablesAndColumnsToAdd | Ange tabeller eller kolumner som ska läggas till på synkroniseringsschema. Varje tabell eller kolumn namn måste vara helt avgränsade med schemanamnet. Till exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera namn för tabellen eller kolumnen kan anges och avgränsade med kommatecken (,). |
| $TablesAndColumnsToRemove | Ange tabeller eller kolumner som ska tas bort från sync-schemat. Varje tabell eller kolumn namn måste vara helt avgränsade med schemanamnet. Till exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera namn för tabellen eller kolumnen kan anges och avgränsade med kommatecken (,). |
|||

## <a name="script-explanation"></a>Skriptet förklaring

Den **UpdateSyncSchema** skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommando-specifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Returnerar information om en grupp för synkronisering. |
| [Uppdatera AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Uppdaterar en sync-grupp. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Returnerar information om medlem synkronisering. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Returnerar information om ett synkroniseringsschema. |
| [Uppdatera AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Uppdaterar ett synkroniseringsschema. |
|||

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure PowerShell [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare exempel för SQL Database PowerShell-skript finns i [Azure SQL Database PowerShell-skript](../sql-database-powershell-samples.md).

För mer information om SQL-datasynkronisering, se:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering](../sql-database-sync-data.md)
-   [Konfigurera Azure SQL datasynkronisering](../sql-database-get-started-sql-data-sync.md)
-   [Metodtips för Azure SQL-datasynkronisering](../sql-database-best-practices-data-sync.md)
-   [Övervaka Azure SQL-datasynkronisering med OMS logganalys](../sql-database-sync-monitor-oms.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering](../sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](../sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
