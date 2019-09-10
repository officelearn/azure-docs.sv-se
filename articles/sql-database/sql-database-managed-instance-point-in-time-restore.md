---
title: SQL Database Hanterad instans-återställning av tidpunkt | Microsoft Docs
description: Så här återställer du en databas i en SQL-hanterad instans till en tidigare tidpunkt.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862140"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Återställa en SQL-hanterad instans databas till en tidigare tidpunkt

Med punkt-i-tids återställning (PITR) kan du skapa en databas som en kopia av en annan databas vid en viss tidpunkt tidigare. Den här artikeln beskriver hur du utför en tidpunkts återställning av en databas i en hanterad instans.

Återställning av tidpunkter kan användas i återställnings scenarier, t. ex. incidenter orsakade av fel, felaktiga inlästa data, borttagning av viktiga data och andra problem, samt endast för testning eller granskning. Beroende på databas inställningarna bevaras säkerhets kopiorna under en period på mellan 7 och 35 dagar.

Återställning av tidpunkter kan användas för att:

- Återställ en databas från en befintlig databas.
- Återställ en databas från en borttagen databas.

Med en hanterad instans kan du dessutom använda återställning på plats-i-tid för att: 

- Återställ en databas till samma hanterade instans.
- Återställ en databas till en annan hanterad instans.


> [!NOTE]
> Det går inte att återställa punkt-till-tid för en hel hanterad instans. Vad är möjligt och förklaras i den här artikeln, är en tidpunkts återställning av en databas som finns på en hanterad instans.


## <a name="limitations"></a>Begränsningar

När du återställer till en annan hanterad instans måste båda instanserna finnas i samma prenumeration och region. Återställningar mellan regioner och över prenumerationer stöds inte för närvarande.

> [!WARNING]
> Var försiktig med lagrings storleken för din hanterade instans – beroende på storleken på att återställa data, kan du få slut på instans lagring. Om det inte finns tillräckligt med utrymme för återställda data kan du använda en annan metod.

I följande tabell visas tidpunkter för återställnings scenarier för hanterade instanser:

|           |Återställ befintlig databas| Återställ befintlig databas|Återställ utelämnad databas| Återställ utelämnad databas|
|:----------|:----------|:----------|:----------|:----------|
|Mål| Samma MI|En annan MI |Samma MI|En annan MI |
|Azure Portal| Ja|Nej |Nej|Nej|
|Azure CLI|Ja |Ja |Nej|Nej|
|PowerShell| Ja|Ja |Ja|Ja|


## <a name="restore-existing-database"></a>Återställ befintlig databas

Återställ en befintlig databas till samma instans med hjälp av Azure Portal, PowerShell eller Azure CLI. Återställ en databas till en annan instans med hjälp av PowerShell eller Azure CLI genom att ange den hanterade mål instansen och resurs gruppens egenskaper. Om dessa parametrar inte anges kommer databasen att återställas till den befintliga instansen som standard. Det finns för närvarande inte stöd för att återställa till en annan instans via Azure Portal. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till din hanterade instans och välj den databas som du vill återställa. 
1. Välj **Återställ** på databas sidan. 

    ![Återställ befintlig databas](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. På sidan **Återställ** väljer du tidpunkten för datum och tid i historiken som du vill återställa databasen till.
1. Välj **Bekräfta** för att återställa databasen. Detta startar återställnings processen, som skapar en ny databas och fylls med data från den ursprungliga databasen vid önskad tidpunkt. Mer information om återställnings processen finns i [återställnings tid](sql-database-recovery-using-backups.md#recovery-time). 

1. Sök efter hanterad instans
1. Välj den databas som du vill återställa
1. På databas-skärmen klickar du på återställnings åtgärd
1. På skärmen Återställ väljer du datum och tid för den plats i historik som du återställer databasen till
1. Efter att du bekräftat startar återställnings processen och, beroende på databasens storlek, kommer den nya databasen att skapas och fyllas i med data från den ursprungliga databasen vid önskad tidpunkt. För varaktighet för återställnings processen kontrol lera återställning med hjälp av säkerhets kopierings artikeln.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat, se [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

Om du vill återställa databasen med hjälp av PowerShell uppdaterar du parametrarna med dina värden och kör följande kommando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"
 
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId
 
Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                               -InstanceName $managedInstanceName `
                               -Name $databaseName `
                               -PointInTime $pointInTime `
                               -TargetInstanceDatabaseName $targetDatabase `
```

Om du vill återställa databasen till en annan hanterad instans anger du namnet på mål resurs gruppen och målets hanterade instans namn.  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Mer information finns i [restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har installerat Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill återställa databasen med Azure CLI uppdaterar du parametrarna med dina värden och kör följande kommando:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Om du vill återställa databasen till en annan hanterad instans anger du namnet på mål resurs gruppen och målets hanterade instans namn.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

En detaljerad förklaring av tillgängliga parametrar finns i [hanterad instans-CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas 
 
Det går bara att återställa en borttagen databas med PowerShell. Databasen kan återställas till samma instans eller till en annan instans. 

Om du vill återställa en borttagen databas med hjälp av PowerShell uppdaterar du parametrarna med värdena och kör följande kommando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Om du vill återställa den borttagna databasen till en annan instans ändrar du resurs gruppens namn och namnet på den hanterade instansen.

Plats parametern måste matcha platsen för resurs gruppen och den hanterade instansen.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Skriv över befintlig databas 
 
Om du vill skriva över en befintlig databas måste du också:

1. TA bort den befintliga databasen som du vill skriva över.
1. Byt namn på den återställda data punkten till namnet på den databas som släpptes. 


### <a name="drop-original-database"></a>SLÄPP ursprunglig databas 
 
Att släppa databasen kan göras med Azure Portal, PowerShell eller Azure CLI. 

Du kan också släppa databasen genom att ansluta till den hanterade instansen direkt, starta SQL Server Management Studio (SSMS) och köra nedanstående Transact-SQL-kommando (T-SQL).

```sql
DROP DATABASE WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till din hanterade instans databas: 

- [Virtuell SQL-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-till-plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slutpunkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

I Azure Portal väljer du databasen från den hanterade instansen och väljer **ta bort**.

   ![Återställ befintlig databas](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-kommando för att släppa en befintlig databas från en hanterad instans: 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande Azure CLI-kommando för att släppa en befintlig databas från en hanterad instans: 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>ÄNDRA det nya databas namnet till ursprungligt

Anslut direkt till den hanterade instansen, starta SQL Server Management Studio och kör sedan följande Transact-SQL-fråga (T-SQL) för att ändra namnet på den återställda databasen till den borttagna databas som du avsåg att skriva över. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Använd någon av följande metoder för att ansluta till din hanterade instans databas: 

- [Virtuell SQL-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt-till-plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slutpunkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Nästa steg

Läs mer om [långsiktig kvarhållning](sql-database-long-term-retention.md) och [automatiserade säkerhets kopieringar](sql-database-automated-backups.md). 
