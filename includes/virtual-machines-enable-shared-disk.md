---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008373"
---
## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Diskstorlekar

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Distribuera delade diskar

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Distribuera en premium-SSD som en delad disk

Om du vill distribuera en hanterad disk med `maxShares` funktionen delad disk aktiverad använder du den nya egenskapen och definierar ett värde som är större än 1. Detta gör disken delbar över flera virtuella datorer.

> [!IMPORTANT]
> Värdet `maxShares` på kan bara ställas in eller ändras när en disk har avmonterats från alla virtuella datorer. Se [Diskstorlekarna](#disk-sizes) för de `maxShares`tillåtna värdena för .

Innan du använder följande `[parameters('dataDiskName')]` `[resourceGroup().location]`mall `[parameters('dataDiskSizeGB')]`ersätter du , och `[parameters('maxShares')]` med dina egna värden.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Distribuera en ultradisk som en delad disk

#### <a name="cli"></a>CLI

Om du vill distribuera en hanterad disk `maxShares` med funktionen delad disk aktiverad ändrar du parametern till ett värde som är större än 1. Detta gör disken delbar över flera virtuella datorer.

> [!IMPORTANT]
> Värdet `maxShares` på kan bara ställas in eller ändras när en disk har avmonterats från alla virtuella datorer. Se [Diskstorlekarna](#disk-sizes) för de `maxShares`tillåtna värdena för .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Om du vill distribuera en hanterad disk med `maxShares` funktionen delad disk aktiverad använder du egenskapen och definierar ett värde som är större än 1. Detta gör disken delbar över flera virtuella datorer.

> [!IMPORTANT]
> Värdet `maxShares` på kan bara ställas in eller ändras när en disk har avmonterats från alla virtuella datorer. Se [Diskstorlekarna](#disk-sizes) för de `maxShares`tillåtna värdena för .

Innan du använder följande `[parameters('dataDiskName')]` `[resourceGroup().location]`mall `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]`ersätter du , , `[parameters('diskIOPSReadWrite')]`, , `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]`och `[parameters('diskMBpsReadOnly')]` med dina egna värden.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Använda delade Azure-diskar med dina virtuella datorer

När du har distribuerat `maxShares>1`en delad disk med kan du montera disken på en eller flera av dina virtuella datorer.

> [!IMPORTANT]
> Alla virtuella datorer som delar en disk måste distribueras i samma [närhetsplaceringsgrupp](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

När du har monterat den delade disken på dina virtuella datorer i klustret kan du upprätta kvorum och läsa/skriva till disken med SCSI PR. Följande PR-kommandon är tillgängliga när du använder delade Azure-diskar:

Om du vill interagera med disken börjar du med listan för beständiga reservationsåtgärder:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

När du använder PR_RESERVE, PR_PREEMPT_RESERVATION eller PR_RELEASE_RESERVATION anger du någon av följande beständiga reservationstyp:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Du måste också ange en beständig reservationsnyckel när du använder PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION eller PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Nästa steg

Om du är intresserad av att prova delade diskar registrerar [du dig för vår förhandsgranskning.](https://aka.ms/AzureSharedDiskPreviewSignUp)