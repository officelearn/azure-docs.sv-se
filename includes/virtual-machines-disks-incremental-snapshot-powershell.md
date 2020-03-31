---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299471"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Regioner som stöds
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell för att skapa en inkrementell ögonblicksbild. Du behöver den senaste versionen av Azure PowerShell, följande kommando kommer antingen att installera den eller uppdatera din befintliga installation till senaste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

När det är installerat, logga in `az login`på din PowerShell-session med .

Om du vill skapa en inkrementell ögonblicksbild med Azure PowerShell anger du `-Incremental` konfigurationen med [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) med `-Snapshot` parametern och skickar den sedan som en variabel till [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) genom parametern.

Ersätt `<yourDiskNameHere>` `<yourResourceGroupNameHere>`, `<yourDesiredSnapShotNameHere>` och med dina värden kan du använda följande skript för att skapa en inkrementell ögonblicksbild:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Du kan identifiera inkrementella ögonblicksbilder `SourceResourceId` från `SourceUniqueId` samma disk med egenskaperna för ögonblicksbilder. `SourceResourceId`är Azure Resource Manager-resurs-ID för den överordnade disken. `SourceUniqueId`är värdet som `UniqueId` ärvts från diskens egenskap. Om du skulle ta bort en disk och sedan skapa en `UniqueId` ny disk med samma namn ändras egenskapens värde.

Du kan `SourceResourceId` `SourceUniqueId` använda och skapa en lista över alla ögonblicksbilder som är associerade med en viss disk. Ersätt `<yourResourceGroupNameHere>` med ditt värde och sedan kan du använda följande exempel för att lista dina befintliga inkrementella ögonblicksbilder:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också använda Azure Resource Manager-mallar för att skapa en inkrementell ögonblicksbild. Du måste se till att apiVersion är inställt på **2019-03-01** och att den inkrementella egenskapen också är inställd på true. Följande kodavsnitt är ett exempel på hur du skapar en inkrementell ögonblicksbild med Resource Manager-mallar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Om du vill se exempelkod som visar differentiella funktioner för inkrementella ögonblicksbilder, med hjälp av .NET, se [Kopiera Azure Managed Disks säkerhetskopior till en annan region med differentiell kapacitet för inkrementella ögonblicksbilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
