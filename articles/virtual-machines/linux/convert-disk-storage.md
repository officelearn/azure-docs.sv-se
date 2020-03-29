---
title: Konvertera hanterad disklagring mellan standard- och premium-SSD
description: Konvertera Azure-hanterad disklagring från standard till premium eller premium till standard med hjälp av Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431492"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konvertera Azure-hanterad disklagring från Standard till Premium eller Premium till Standard

Det finns fyra disktyper av Azure-hanterade diskar: Azure ultra SSDs (förhandsversion), premium SSD, standard SSD och standard HDD. Du kan växla mellan de tre GA-disktyperna (premium SSD, standard-SSD och standard HDD) baserat på dina prestandabehov. Du kan ännu inte byta från eller till en ultra SSD, måste du distribuera en ny.

Den här funktionen stöds inte för ohanterade diskar. Men du kan enkelt [konvertera en ohanterad disk till en hanterad disk](convert-unmanaged-to-managed-disks.md) för att kunna växla mellan disktyper.

Den här artikeln visar hur du konverterar hanterade diskar från Standard till Premium eller Premium till Standard med hjälp av Azure CLI. Information om hur du installerar eller uppgraderar verktyget finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

* Diskkonvertering kräver en omstart av den virtuella datorn (VM), så schemalägg migreringen av disklagringen under ett befintligt underhållsfönster.
* För ohanterade diskar [konverterar](convert-unmanaged-to-managed-disks.md) du först till hanterade diskar så att du kan växla mellan lagringsalternativ.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Växla alla hanterade diskar i en virtuell dator mellan Premium och Standard

I det här exemplet visas hur du konverterar alla en virtuell dators diskar från Standard till Premium-lagring eller från Premium till Standard-lagring. Om du vill använda Premium-hanterade diskar måste den virtuella datorn använda en [vm-storlek](sizes.md) som stöder Premium-lagring. Det här exemplet växlar också till en storlek som stöder Premium-lagring.

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

För din utvecklings-/testarbetsbelastning kanske du vill ha en blandning av Standard- och Premium-diskar för att minska dina kostnader. Du kan välja att bara uppgradera de diskar som behöver bättre prestanda. I det här exemplet visas hur du konverterar en enda VM-disk från Standard till Premium-lagring eller från Premium till Standard-lagring. Om du vill använda Premium-hanterade diskar måste den virtuella datorn använda en [vm-storlek](sizes.md) som stöder Premium-lagring. Det här exemplet växlar också till en storlek som stöder Premium-lagring.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Växla hanterade diskar mellan standard HDD och Standard SSD

Det här exemplet visar hur du konverterar en enda VM-disk från standard HDD till Standard SSD eller från Standard SSD till Standard HDD.

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

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella datorn i listan över **virtuella datorer**.
3. Om den virtuella datorn inte stoppas väljer du **Stoppa** högst upp i fönstret **Översikt** för den virtuella datorn och väntar på att den virtuella datorn ska stoppas.
4. Välj **Diskar** på menyn i fönstret för den virtuella datorn.
5. Markera den disk som du vill konvertera.
6. Välj **Konfiguration** på menyn.
7. Ändra **kontotypen** från **standard hdd** till **Premium SSD** eller från **Premium SSD** till **Standard HDD**.
8. Välj **Spara**och stäng diskfönstret.

Uppdateringen av disktypen är ögonblicklig. Du kan starta om den virtuella datorn efter konverteringen.

## <a name="next-steps"></a>Nästa steg

Gör en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).
