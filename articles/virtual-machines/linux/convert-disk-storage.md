---
title: Konvertera Azure managed disks-lagring från standard till premium, och vice versa | Microsoft Docs
description: Så här konverterar du Azure managed disks-lagring från standard till premium och vice versa med hjälp av Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 10dc7a2c7e4de44979ec72b1d292c69866e1faae
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326416"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konvertera Azure managed disks-lagring från standard till premium, och vice versa

Hanterade diskar erbjuder fyra [disktyp](disks-types.md) alternativ: Ultra solid state-hårddisk (SSD), Premium SSD, standarder SSD och Standard hårddisk lagringsmedium. Det kan du enkelt växla mellan alternativen med minimal avbrottstid utifrån dina behov. Det finns inte stöd för ohanterade diskar. Men du kan enkelt [konvertera till managed disks](convert-unmanaged-to-managed-disks.md) enkelt växla mellan disktyper.

Den här artikeln visar hur du konverterar hanterade diskar från standard till premium och vice versa med hjälp av Azure CLI. Om du behöver installera eller uppgradera den kan du läsa [installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Innan du börjar

* Konverteringen kräver en omstart av den virtuella datorn, så du bör schemalägga migreringen av din disklagring under en schemalagd underhållstid. 
* Om du använder ohanterade diskar först [konvertera till managed disks](convert-unmanaged-to-managed-disks.md) du använder den här artikeln för att växla mellan lagringsalternativ.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konvertera alla hanterade diskar på en virtuell dator från standard till premium, och vice versa

I följande exempel visas hur du växlar alla diskar på en virtuell dator från standard till premium storage. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konvertera en hanterad disk från standard till premium, och vice versa

Du kanske vill ha blandning av standard och premium-diskar för att minska dina kostnader för utveckling/testning-arbetsbelastning. Du kan göra det genom att uppgradera till premium storage kan endast diskar som behöver bättre prestanda. I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard till premium storage och vice versa. Om du vill använda premium-hanterade diskar, den virtuella datorn måste använda en [VM-storlek](sizes.md) som har stöd för premiumlagring. Det här exemplet växlar också till en storlek som stöder premiumlagring.

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Konvertera en hanterad disk från standard HDD till standard SSD och vice versa

I följande exempel visas hur du växlar en enskild disk för en virtuell dator från standard-Hårddisk till SSD som standard.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Konvertera med hjälp av Azure portal

Du kan också konvertera ohanterade diskar till hanterade diskar med Azure portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den virtuella datorn från listan över virtuella datorer i portalen.
3. I bladet för den virtuella datorn, väljer **diskar** på menyn.
4. Överst på den **diskar** bladet väljer **migrera till managed disks**.
5. Om den virtuella datorn är i en tillgänglighetsuppsättning, är en varning på den **migrera till managed disks** bladet som du vill omvandla tillgänglighetsuppsättningen först. Varningen ska ha en länk som du kan klicka på för att omvandla tillgänglighetsuppsättningen. När tillgänglighetsuppsättningen har omvandlats eller om den virtuella datorn inte är i en tillgänglighetsuppsättning klickar du på **migrera** att starta processen med att migrera dina diskar till hanterade diskar. 

Den virtuella datorn stoppas och startas om när migreringen är klar.

## <a name="next-steps"></a>Nästa steg

Ta en skrivskyddad kopia av en virtuell dator med hjälp av [ögonblicksbilder](snapshot-copy-managed-disk.md).

