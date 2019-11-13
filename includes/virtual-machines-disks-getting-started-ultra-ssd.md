---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3525edb2a73811254b2a4dce70ce3edb58988492
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012440"
---
Azure Ultra disks erbjuder högt data flöde, hög IOPS och konsekvent låg latens disk lagring för virtuella Azure IaaS-datorer (VM). Det nya erbjudandet ger överst i linje prestanda på samma tillgänglighets nivå som våra befintliga diskar. En stor fördel med Ultra disks är möjligheten att dynamiskt ändra prestanda för SSD tillsammans med dina arbets belastningar utan att behöva starta om dina virtuella datorer. Ultra disks lämpar sig för data intensiva arbets belastningar som SAP HANA, toppnivå databaser och transaktions krävande arbets belastningar.

## <a name="ga-scope-and-limitations"></a>Allmän omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Bestämma VM-storlek och region tillgänglighet

Om du vill använda Ultra disks måste du bestämma vilken tillgänglighets zon du befinner dig i. Alla regioner stöder inte varje VM-storlek med Ultra disks. Du kan kontrol lera om din region, zon och VM-storlek stöder Ultra disks genom att köra något av följande kommandon, se till att ersätta **regions**-, **vmSize**-och **prenumerations** värden först:

CLI

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Svaret liknar det formulär som visas nedan, där X är den zon som ska användas för att distribuera i den valda regionen. X kan vara antingen 1, 2 eller 3.

Behåll **zonens värde,** det motsvarar din tillgänglighets zon och du behöver den för att kunna distribuera en Ultra-disk.

|ResourceType  |Namn  |Plats  |Zoner  |Begränsning  |Funktion  |Värde  |
|---------|---------|---------|---------|---------|---------|---------|
|disk     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Om det inte fanns något svar från kommandot, stöds inte den valda virtuella dator storleken med Ultra disks i den valda regionen.

Nu när du vet vilken zon du ska distribuera till, följer du distributions stegen i den här artikeln för att antingen distribuera en virtuell dator med en mycket disk ansluten eller ansluta en Ultra disk till en befintlig virtuell dator.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuera en Ultra disk med Azure Resource Manager

Ta först reda på vilken VM-storlek som ska distribueras. En lista över VM-storlekar som stöds finns i avsnittet om [omfång och begränsningar](#ga-scope-and-limitations) .

Om du vill skapa en virtuell dator med flera Ultra disks, se exemplet [skapa en virtuell dator med flera Ultra-diskar](https://aka.ms/ultradiskArmTemplate).

Om du tänker använda din egen mall kontrollerar du att **API version** för `Microsoft.Compute/virtualMachines` och `Microsoft.Compute/Disks` har angetts som `2018-06-01` (eller senare).

Ange disk-SKU: n till **UltraSSD_LRS**, ange disk kapacitet, IOPS, tillgänglighets zon och data flöde i Mbit/s för att skapa en Ultra-disk.

När den virtuella datorn har allokerats kan du partitionera och formatera data diskarna och konfigurera dem för dina arbets belastningar.

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuera en Ultra disk med CLI

Ta först reda på vilken VM-storlek som ska distribueras. I avsnittet allmän [omfattning och begränsningar](#ga-scope-and-limitations) finns en lista över VM-storlekar som stöds.

Du måste skapa en virtuell dator som kan använda Ultra disks för att ansluta en Ultra disk.

Ersätt eller ange **$VMName**, **$rgname**, **$diskname**, **$location**, **$Password**, **$User** variabler med dina egna värden. Ange **$Zone** till värdet för din tillgänglighets zon som du fick från [början av den här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande CLI-kommando för att skapa en ultra-aktiverad virtuell dator:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Skapa en Ultra disk med CLI

Nu när du har en virtuell dator som kan ansluta till Ultra disks kan du skapa och ansluta en Ultra disk till den.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Ansluta en Ultra disk till en virtuell dator med CLI

Alternativt, om din befintliga virtuella dator finns i en region/tillgänglighets zon som kan använda Ultra disks, kan du använda Ultra disks utan att behöva skapa en ny virtuell dator.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Justera prestanda för en Ultra disk med CLI

Ultra disks är en unik funktion som du kan använda för att justera prestandan, följande kommando visar hur du använder den här funktionen:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Distribuera en Ultra disk med PowerShell

Ta först reda på vilken VM-storlek som ska distribueras. I avsnittet allmän [omfattning och begränsningar](#ga-scope-and-limitations) finns en lista över VM-storlekar som stöds. för ytterligare information om de här VM-storlekarna.

Om du vill använda Ultra disks måste du skapa en virtuell dator som kan använda Ultra disks. Ersätt eller ange **$resourcegroup** och **$vmName** variabler med dina egna värden. Ange **$Zone** till värdet för din tillgänglighets zon som du fick från [början av den här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande [New-AzVm-](/powershell/module/az.compute/new-azvm) kommando för att skapa en ultra-aktiverad virtuell dator:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>Skapa en Ultra disk med PowerShell

Nu när du har en virtuell dator som kan använda Ultra disks kan du skapa och ansluta en Ultra disk till den:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Ansluta en Ultra disk till en virtuell dator med hjälp av PowerShell

Alternativt, om din befintliga virtuella dator finns i en region/tillgänglighets zon som kan använda Ultra disks, kan du använda Ultra disks utan att behöva skapa en ny virtuell dator.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Justera prestanda för en Ultra disk med hjälp av PowerShell

Ultra disks har en unik funktion som gör att du kan justera prestandan, följande kommando är ett exempel som justerar prestandan utan att behöva koppla från disken:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Nästa steg

Om du vill prova den nya disk typen [begär åtkomst med den här undersökningen](https://aka.ms/UltraDiskSignup).