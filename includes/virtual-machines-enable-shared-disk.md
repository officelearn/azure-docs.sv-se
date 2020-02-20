---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471722"
---
## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Disk storlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Distribuera en Azure-delad disk

Om du vill distribuera en hanterad disk med funktionen delad disk aktive rad använder du den nya egenskapen `maxShares` och definierar ett värde `>1`. Detta gör disk delningen över flera virtuella datorer.

> [!IMPORTANT]
> Värdet för `maxShares` kan bara ställas in eller ändras när en disk demonteras från alla virtuella datorer. Se [disk storlekarna](#disk-sizes) för de tillåtna värdena för `maxShares`.

Innan du använder följande mall ersätter du `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`och `[parameters('maxShares')]` med dina egna värden.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Använda Azure delade diskar med dina virtuella datorer

När du har distribuerat en delad disk med `maxShares>1`kan du montera disken till en eller flera av dina virtuella datorer.

> [!IMPORTANT]
> Alla virtuella datorer som delar en disk måste distribueras i samma [närhets placerings grupp](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>SCSI PR-kommandon som stöds

När du har monterat den delade disken till dina virtuella datorer i klustret kan du upprätta kvorum och läsa/skriva till disken med SCSI PR. Följande PR-kommandon är tillgängliga när du använder Azure delade diskar:

Om du vill interagera med disken börjar du med listan persistent-reservation-åtgärd:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

När du använder PR_RESERVE, PR_PREEMPT_RESERVATION eller PR_RELEASE_RESERVATION anger du någon av följande beständiga reservations typer:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Du måste också ange en permanent reservations nyckel när du använder PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION eller PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Nästa steg

Om du är intresse rad av att prova delade diskar kan du [Registrera dig för vår för hands version](https://aka.ms/AzureSharedDiskPreviewSignUp).