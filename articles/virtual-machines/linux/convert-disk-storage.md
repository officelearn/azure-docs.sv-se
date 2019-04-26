---
title: Konvertera Azure managed disks-lagring från Standard till Premium- eller Premium till Standard | Microsoft Docs
description: Så här konverterar du Azure managed disks-lagring från Standard till Premium- eller Premium till Standard med hjälp av Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328790"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konvertera Azure managed disks-lagring från Standard till Premium- eller Premium till Standard

Det finns fyra [disktyper](disks-types.md) för Azure-hanterade diskar: Azure Ultra disklagring-, Premium SSD-, Standard SSD- och Standard HDD. Du kan enkelt växla mellan Premium SSD-, Standard SSD- och Standard HDD-baserade på dina behov med lite avbrott. Den här funktionen stöds inte för ohanterade diskar eller Ultra disklagring. Men du kan enkelt [convert ohanterade till hanterade diskar](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disktyper.

Den här artikeln visar hur du konverterar hanterade diskar från Standard till Premium- eller Premium till Standard med hjälp av Azure CLI. Om du vill installera eller uppgradera verktyget, se [installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

* Omvandla kräver en omstart av den virtuella datorn (VM), så du bör schemalägga migreringen av din disklagring under en schemalagd underhållstid.
* För ohanterade diskar första [konvertera till managed disks](convert-unmanaged-to-managed-disks.md) så att du kan växla mellan lagringsalternativ.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Växla alla hanterade diskar på en virtuell dator mellan Premium och Standard

Det här exemplet visar hur du konverterar alla diskar på en virtuell dator från Standard till Premium storage eller från Premium till Standard storage. Om du vill använda Premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Växla enskilda hanterade diskar mellan Standard och Premium

För utveckling/testning-arbetsbelastning kanske du vill ha en blandning av Standard och Premium-diskar för att minska kostnaderna. Du kan välja att uppgradera de diskar som behöver bättre prestanda. Det här exemplet visar hur du konverterar en enskild datordisk för virtuell från Standard till Premium storage eller från Premium till Standard storage. Om du vill använda Premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Växla hanterade diskar mellan Standard HDD- och Standard SSD

Det här exemplet visar hur du konverterar en enskild datordisk för virtuell från Standard Hårddisk till Standard SSD eller från Standard SSD till Standard HDD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Växla hanterade diskar mellan Standard och Premium i Azure-portalen

Följ de här stegen:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den virtuella datorn från listan över **virtuella datorer**.
3. Om den virtuella datorn inte är stoppad väljer **stoppa** överst på den virtuella datorn **översikt** fönstret och vänta tills den virtuella datorn att stoppa.
4. I fönstret för den virtuella datorn, väljer **diskar** på menyn.
5. Välj den disk som du vill konvertera.
6. Välj **Configuration** på menyn.
7. Ändra den **kontotyp** från **Standard HDD** till **Premium SSD** eller från **Premium SSD** till **Standard HDD**.
8. Välj **spara**, och Stäng fönstret disk.

Uppdatering av typ av disk är omedelbart. Du kan starta om den virtuella datorn efter konverteringen.

## <a name="next-steps"></a>Nästa steg

Skapa en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).
