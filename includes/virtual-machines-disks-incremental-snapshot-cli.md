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
ms.openlocfilehash: 4bdeef537556db94338ed50fcfa6e9d88431f25a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016273"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Du kan skapa en stegvis ögonblicks bild med Azure CLI, du behöver den senaste versionen av Azure CLI. 

I Windows installerar eller uppdaterar du den befintliga installationen till den senaste versionen med följande kommando:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
I Linux kan CLI-installationen variera beroende på operativ systemets version.  Se [Installera Azure CLI](/cli/azure/install-azure-cli) för din specifika Linux-version.

Om du vill skapa en stegvis ögonblicks bild använder du [AZ Snapshot Create](/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) med `--incremental` parametern.

I följande exempel skapas en stegvis ögonblicks bild, ersätter `<yourDesiredSnapShotNameHere>` ,, `<yourResourceGroupNameHere>` `<exampleDiskName>` och `<exampleLocation>` med dina egna värden, och sedan körs exemplet:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Du kan identifiera stegvisa ögonblicks bilder från samma disk med `SourceResourceId` `SourceUniqueId` egenskaperna och för ögonblicks bilder. `SourceResourceId` är Azure Resource Manager resurs-ID för den överordnade disken. `SourceUniqueId` är värdet som ärvs från `UniqueId` diskens egenskap. Om du skulle ta bort en disk och sedan skapa en ny disk med samma namn, `UniqueId` ändras egenskapens värde.

Du kan använda `SourceResourceId` och `SourceUniqueId` för att skapa en lista över alla ögonblicks bilder som är associerade med en viss disk. I följande exempel visas en lista över alla stegvisa ögonblicks bilder som är associerade med en viss disk, men den kräver vissa inställningar.

I det här exemplet används JQ för att fråga efter data. Du måste [Installera JQ](https://stedolan.github.io/jq/download/)för att kunna köra exemplet.

Ersätt `<yourResourceGroupNameHere>` och `<exampleDiskName>` med dina värden kan du använda följande exempel för att visa en lista över dina befintliga stegvisa ögonblicks bilder, förutsatt att du även har installerat JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också använda Azure Resource Manager mallar för att skapa en stegvis ögonblicks bild. Du måste kontrol lera att API version är inställt på **2019-03-01** och att den stegvisa egenskapen också har angetts till true. Följande fragment är ett exempel på hur du skapar en stegvis ögonblicks bild med Resource Manager-mallar:

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

Om du vill se exempel kod som demonstrerar den differentiella kapaciteten för stegvisa ögonblicks bilder med hjälp av .NET, se [Kopiera Azure-Managed disks säkerhets kopior till en annan region med differentiella funktioner i stegvisa ögonblicks bilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).