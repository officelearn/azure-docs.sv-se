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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343035"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Du kan skapa en inkrementell ögonblicksbild med Azure CLI, behöver du den senaste versionen av Azure CLI. 

I Windows installeras eller uppdateras den befintliga installationen till den senaste versionen av följande kommando:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
På Linux varierar CLI-installationen beroende på operativsystemversion.  Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för just din Linux-version.

Om du vill skapa en inkrementell ögonblicksbild använder du [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) med parametern. `--incremental`

I följande exempel skapas en `<yourDesiredSnapShotNameHere>`inkrementell ögonblicksbild, ersätter , `<yourResourceGroupNameHere>``<exampleDiskName>`och `<exampleLocation>` med dina egna värden och kör sedan exemplet:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Du kan identifiera inkrementella ögonblicksbilder `SourceResourceId` från `SourceUniqueId` samma disk med egenskaperna för ögonblicksbilder. `SourceResourceId`är Azure Resource Manager-resurs-ID för den överordnade disken. `SourceUniqueId`är värdet som `UniqueId` ärvts från diskens egenskap. Om du skulle ta bort en disk och sedan skapa en `UniqueId` ny disk med samma namn ändras egenskapens värde.

Du kan `SourceResourceId` `SourceUniqueId` använda och skapa en lista över alla ögonblicksbilder som är associerade med en viss disk. I följande exempel visas alla inkrementella ögonblicksbilder som är associerade med en viss disk, men det kräver vissa inställningar.

I det här exemplet används jq för att fråga efter data. Om du vill köra exemplet måste du [installera jq](https://stedolan.github.io/jq/download/).

Ersätt `<yourResourceGroupNameHere>` `<exampleDiskName>` och med dina värden, sedan kan du använda följande exempel för att lista dina befintliga inkrementella ögonblicksbilder, så länge du också har installerat jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
