---
title: "Konvertera Azure hanterade diskar lagring från standard till premium, och vice versa | Microsoft Docs"
description: "Så här konverterar du Azure hanterade diskar lagring från standard till premium och vice versa med hjälp av Azure CLI."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konvertera Azure hanterade diskar lagring från standard till premium, och vice versa

Hanterade diskar erbjuder två alternativ för lagring: [Premium](../../storage/storage-premium-storage.md) (SSD-baserad) och [Standard](../../storage/storage-standard-storage.md) (HDD-baserat). På så sätt kan du enkelt växla mellan de två alternativen med minimal avbrottstid baserat på dina prestandabehov. Den här funktionen är inte tillgänglig för ohanterade diskar. Men du kan enkelt [konvertera till hanterade diskar](convert-unmanaged-to-managed-disks.md) enkelt växla mellan de två alternativen.

Den här artikeln visar hur du konverterar hanterade diskar från standard till premium och vice versa med hjälp av Azure CLI. Om du behöver installera eller uppgradera den, se [installera Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Innan du börjar

* Konverteringen kräver en omstart av den virtuella datorn, så Schemalägg migrering av lagringsenheterna diskar under en befintlig underhållsperiod. 
* Om du använder ohanterade diskar först [konvertera till hanterade diskar](convert-unmanaged-to-managed-disks.md) för att växla mellan två alternativ för lagring med hjälp av den här artikeln. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konvertera alla hanterade diskar på en virtuell dator från standard till premium, och vice versa

I följande exempel visar vi hur du växla alla diskar på en virtuell dator från standard till premium-lagring. Du använder premiumdiskar hanteras genom den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premium-lagring. Det här exemplet växlar också till en storlek som har stöd för premium-lagring.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konverterar en hanterad disk från standard till premium, och vice versa

För din arbetsbelastning för utveckling och testning, kanske du vill ha blandning av standard och premium diskar för att minska dina kostnader. Du kan göra det genom att uppgradera till premium-lagring på diskar som kräver bättre prestanda. I följande exempel visar vi hur du växlar en enda disk av en virtuell dator från standard till premium-lagring och vice versa. Du använder premiumdiskar hanteras genom den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premium-lagring. Det här exemplet växlar också till en storlek som har stöd för premium-lagring.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Nästa steg

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

