---
title: Hanterad instans - Point-in-time-återställning (PITR)
description: Återställa en SQL-databas i en hanterad instans till en tidigare tidpunkt.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268813"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Återställa en SQL-databas i en hanterad instans till en tidigare tidpunkt

Använd point-in-time restore (PITR) för att skapa en databas som en kopia av en annan databas från en tid tidigare. I den här artikeln beskrivs hur du gör en point-in-time-återställning av en databas i en hanterad Azure SQL-databas-hanterad instans.

Point-in-time-återställning är användbart i återställningsscenarier, till exempel incidenter som orsakas av fel, felaktigt inlästa data eller radering av viktiga data. Du kan också använda den helt enkelt för testning eller granskning. Säkerhetskopieringsfiler sparas i 7 till 35 dagar, beroende på databasinställningarna.

Point-in-time-återställning kan återställa en databas:

- från en befintlig databas.
- från en borttagen databas.
- samma hanterade instans eller till en annan hanterad instans. 

## <a name="limitations"></a>Begränsningar

Tidsåterställning till en hanterad instans har följande begränsningar:

- När du återställer från en hanterad instans till en annan måste båda instanserna finnas i samma prenumeration och region. Återställning mellan regioner och flera prenumerationer stöds för närvarande inte.
- Point-in-time-återställning av en hel hanterad instans är inte möjlig. I den här artikeln förklaras bara vad som är möjligt: point-in-time-återställning av en databas som finns på en hanterad instans.

> [!WARNING]
> Tänk på lagringsstorleken för din hanterade instans. Beroende på storleken på de data som ska återställas kan instanslagringen ta. Om det inte finns tillräckligt med utrymme för återställda data använder du en annan metod.

I följande tabell visas scenarier för tidsåterställning för hanterade instanser:

|           |Återställa befintlig DB till samma hanterade instans| Återställa befintlig DB till en annan hanterad instans|Återställa tappade DB till samma hanterade instans|Återställa bortsläppt DB till en annan hanterad instans|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portal**| Ja|Inga |Ja|Inga|
|**Azure CLI**|Ja |Ja |Inga|Inga|
|**Powershell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Återställa en befintlig databas

Återställa en befintlig databas till samma instans med hjälp av Azure-portalen, PowerShell eller Azure CLI. Om du vill återställa en databas till en annan instans använder du PowerShell eller Azure CLI så att du kan ange egenskaperna för målhanterad instans och resursgrupp. Om du inte anger dessa parametrar återställs databasen till den befintliga instansen som standard. Azure-portalen stöder för närvarande inte återställning till en annan instans.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Gå till den hanterade instansen och välj den databas som du vill återställa.
3. Välj **Återställ** på databassidan:

    ![Återställa en databas med hjälp av Azure-portalen](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. På sidan **Återställ** väljer du punkten för det datum och den tid som du vill återställa databasen till.
5. Välj **Bekräfta** om du vill återställa databasen. Den här åtgärden startar återställningsprocessen, som skapar en ny databas och fyller den med data från den ursprungliga databasen vid den angivna tidpunkten. Mer information om återställningsprocessen finns i [Återställningstid](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du inte redan har Azure PowerShell installerat läser du [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps).

Om du vill återställa databasen med PowerShell anger du värdena för parametrarna i följande kommando. Kör sedan kommandot:

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

Om du vill återställa databasen till en annan hanterad instans anger du även namnen på målresursgruppen och målhanterad instans:  

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

Mer information finns i [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du inte redan har Azure CLI installerat läser du [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Om du vill återställa databasen med hjälp av Azure CLI anger du dina värden för parametrarna i följande kommando. Kör sedan kommandot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Om du vill återställa databasen till en annan hanterad instans anger du även namnen på målresursgruppen och hanterad instans:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

En detaljerad förklaring av tillgängliga parametrar finns i [CLI-dokumentationen för att återställa en databas i en hanterad instans](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas

Återställning av en borttagen databas kan göras med hjälp av PowerShell eller Azure-portalen. Om du vill återställa en borttagen databas till samma instans använder du antingen Azure-portalen eller PowerShell. Om du vill återställa en borttagen databas till en annan instans använder du PowerShell. 

### <a name="portal"></a>Portalen 


Om du vill återställa en hanterad databas med Azure-portalen öppnar du översiktssidan för hanterade instanser och väljer **Borttagna databaser**. Välj en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data återställda från säkerhetskopian.

  ![Skärmbild av den borttagna Azure SQL-instansdatabasen](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Om du vill återställa en databas till samma instans uppdaterar du parametervärdena och kör sedan följande PowerShell-kommando: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Om du vill återställa databasen till en annan hanterad instans anger du även namnen på målresursgruppen och målhanterad instans:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Skriva över en befintlig databas

Om du vill skriva över en befintlig databas måste du:

1. Släpp den befintliga databas som du vill skriva över.
2. Byt namn på den point-in-time-restaurerade databasen till namnet på databasen som du har tappat.

### <a name="drop-the-original-database"></a>Släpp den ursprungliga databasen

Du kan släppa databasen med hjälp av Azure-portalen, PowerShell eller Azure CLI.

Du kan också släppa databasen genom att ansluta till den hanterade instansen direkt, starta SQL Server Management Studio (SSMS) och sedan köra följande Transact-SQL-kommando (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i den hanterade instansen:

- [SSMS/Azure Data Studio via en virtuell Azure-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt till plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slutpunkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure-portalen väljer du databasen från den hanterade instansen och väljer sedan **Ta bort**.

   ![Ta bort en databas med hjälp av Azure-portalen](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Använd följande PowerShell-kommando för att släppa en befintlig databas från en hanterad instans:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande Azure CLI-kommando för att släppa en befintlig databas från en hanterad instans:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Ändra det nya databasnamnet så att det matchar det ursprungliga databasnamnet

Anslut direkt till den hanterade instansen och starta SQL Server Management Studio. Kör sedan följande Transact-SQL-fråga (T-SQL). Frågan ändrar namnet på den återställda databasen till namnet på den bortsläppta databasen som du tänker skriva över.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Använd någon av följande metoder för att ansluta till databasen i den hanterade instansen:

- [Virtuell Azure-dator](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punkt till plats](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Offentlig slutpunkt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Nästa steg

Läs mer om [automatiska säkerhetskopior](sql-database-automated-backups.md).
