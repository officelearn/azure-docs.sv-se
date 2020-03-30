---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117110"
---
Azure ultra diskar erbjuder högt dataflöde, hög IOPS och konsekvent låg latens disklagring för Azure IaaS virtuella datorer (VMs). Det här nya erbjudandet ger förstklassig prestanda på samma tillgänglighetsnivåer som våra befintliga diskerbjudanden. En stor fördel med ultradiskar är möjligheten att dynamiskt ändra SSD:s prestanda tillsammans med dina arbetsbelastningar utan att behöva starta om dina virtuella datorer. Ultradiskar är lämpliga för dataintensiva arbetsbelastningar som SAP HANA, databaser på toppnivå och transaktionskrävande arbetsbelastningar.

## <a name="ga-scope-and-limitations"></a>GA omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Bestäm vm-storlek och regiontillgänglighet

Om du vill utnyttja ultradiskar måste du bestämma vilken tillgänglighetszon du befinner dig i. Alla regioner stöder inte alla virtuella datorer med ultradiskar. Om du vill ta reda på om storleken på din region, zon och virtuell dator stöder ultradiskar kör du något av följande kommandon genom att först ersätta **regionen,** **vmSize**och prenumerationsvärdena: **subscription**

Cli:

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Svaret liknar formuläret nedan, där X är den zon som ska användas för distribution i den valda regionen. X kan vara antingen 1, 2 eller 3.

Bevara **zonvärdet,** det representerar din tillgänglighetszon och du behöver den för att kunna distribuera en Ultra-disk.

|ResourceType  |Namn  |Location  |Zoner  |Begränsning  |Funktion  |Värde  |
|---------|---------|---------|---------|---------|---------|---------|
|Diskar     |UltraSSD_LRS         |eastus2 (östra)         |X         |         |         |         |

> [!NOTE]
> Om det inte fanns något svar från kommandot stöds inte den valda vm-storleken med ultradiskar i den valda regionen.

Nu när du vet vilken zon som ska distribueras till följer du distributionsstegen i den här artikeln för att antingen distribuera en virtuell dator med en ultradisk ansluten eller ansluta en ultradisk till en befintlig virtuell dator.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuera en ultradisk med Azure Resource Manager

Bestäm först den virtuella datorns storlek som ska distribueras. En lista över vm-storlekar som stöds finns i avsnittet [GA-scope och begränsningar.](#ga-scope-and-limitations)

Om du vill skapa en virtuell dator med flera ultradiskar läser du exemplet [Skapa en virtuell dator med flera ultradiskar](https://aka.ms/ultradiskArmTemplate).

Om du tänker använda din egen mall kontrollerar `Microsoft.Compute/Disks` du `2018-06-01` att **apiVersion** för `Microsoft.Compute/virtualMachines` och är inställt som (eller senare).

Ställ in disken sku att **UltraSSD_LRS**, ange sedan diskkapacitet, IOPS, tillgänglighetszon och dataflöde i MBps för att skapa en ultra disk.

När den virtuella datorn har etablerats kan du partitionera och formatera datadiskarna och konfigurera dem för dina arbetsbelastningar.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Distribuera en ultradisk med Azure-portalen

Det här avsnittet omfattar distribution av en virtuell dator som är utrustad med en ultradisk som en datadisk. Det förutsätter att du har förtrogenhet med att distribuera en virtuell dator, om du inte gör det, se vår [Snabbstart: Skapa en virtuell Windows-dator i Azure-portalen](../articles/virtual-machines/windows/quick-create-portal.md).

- Logga in på [Azure-portalen](https://portal.azure.com/) och navigera för att distribuera en virtuell dator (VM).
- Se till att välja en [vm-storlek och region som stöds](#ga-scope-and-limitations).
- Välj **tillgänglighetszon** i **tillgänglighetsalternativ**.
- Fyll i de återstående posterna med val av val.
- Välj **Diskar**.

![skapa-ultra-disk-aktiverad-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- På bladet Diskar väljer du **Ja** för **aktivera Ultra Disk-kompatibilitet**.
- Välj **Skapa och bifoga en ny disk** för att ansluta en ultradisk nu.

![aktivera och ansluta-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Ange ett namn på skapa **ett nytt diskblad** och välj sedan **Ändra storlek**.
- Ändra **kontotypen** till **Ultra Disk**.
- Ändra värdena **för Anpassad diskstorlek (GiB),** **Disk IOPS**och **Diskdataflöde** till valfria värden.
- Välj **OK** i båda bladen.
- Fortsätt med vm-distributionen, kommer det att vara samma som du skulle distribuera någon annan virtuell dator.

![skapa-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Bifoga en ultradisk med Azure-portalen

Alternativt, om din befintliga virtuella dator är i en region / tillgänglighet zon som kan använda ultra diskar, kan du använda ultra diskar utan att behöva skapa en ny virtuell dator. Genom att aktivera ultradiskar på den befintliga virtuella datorn och sedan koppla dem som datadiskar.

- Navigera till den virtuella datorn och välj **Diskar**.
- Välj **Redigera**.

![alternativ-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Välj **Ja** för **aktivera Ultra Disk-kompatibilitet**.

![ultra-alternativ-ja-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Välj **Spara**.
- Välj **Lägg till datadisk** och välj **sedan Skapa disk**i listrutan för **Namn** .

![skapa och bifoga-ny-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Fyll i ett namn på den nya disken och välj sedan **Ändra storlek**.
- Ändra **kontotypen** till **Ultra Disk**.
- Ändra värdena **för Anpassad diskstorlek (GiB),** **Disk IOPS**och **Diskdataflöde** till valfria värden.
- Välj **OK** och välj sedan **Skapa**.

![göra-en-ny-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- När du har återsänts till diskens blad väljer du **Spara**.

![spara och bifoga-ny-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Justera prestanda för en ultradisk med Azure-portalen

Ultra diskar erbjuder en unik funktion som gör att du kan justera deras prestanda. Du kan göra dessa justeringar från Azure-portalen, på diskarna själva.

- Navigera till den virtuella datorn och välj **Diskar**.
- Välj den ultraskiva som du vill ändra prestanda för.

![välja-ultra-disk-till-modifiera.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Välj **Konfiguration** och gör sedan ändringarna.
- Välj **Spara**.

![konfigurera-ultra-disk-prestanda och storlek.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Distribuera en ultradisk med CLI

Bestäm först den virtuella datorns storlek som ska distribueras. Se avsnittet [GA-scope och begränsningar](#ga-scope-and-limitations) för en lista över vm-storlekar som stöds.

Du måste skapa en virtuell dator som kan använda ultradiskar för att kunna ansluta en ultradisk.

Ersätt eller ange **$vmname**, **$rgname**, **$diskname** **,** **$location**$password , **$user** variabler med dina egna värden. Ange **$zone** värdet för den tillgänglighetszon som du fick från början av den [här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande CLI-kommando för att skapa en ultraaktiverad virtuell dator:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Skapa en ultradisk med CLI

Nu när du har en virtuell dator som kan ansluta ultra diskar, kan du skapa och bifoga en ultra disk till den.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Koppla en ultradisk till en virtuell dator med CLI

Alternativt, om din befintliga virtuella dator är i en region / tillgänglighet zon som kan använda ultra diskar, kan du använda ultra diskar utan att behöva skapa en ny virtuell dator.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Justera prestanda för en ultradisk med CLI

Ultra diskar erbjuder en unik funktion som gör att du kan justera deras prestanda, visar följande kommando hur du använder den här funktionen:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Distribuera en ultradisk med PowerShell

Bestäm först den virtuella datorns storlek som ska distribueras. Se avsnittet [GA-scope och begränsningar](#ga-scope-and-limitations) för en lista över vm-storlekar som stöds.

Om du vill använda ultradiskar måste du skapa en virtuell dator som kan använda ultradiskar. Ersätt eller ställ in **variablerna $resourcegroup** och **$vmName** med dina egna värden. Ange **$zone** värdet för den tillgänglighetszon som du fick från början av den [här artikeln](#determine-vm-size-and-region-availability). Kör sedan följande [New-AzVm-kommando](/powershell/module/az.compute/new-azvm) för att skapa en ultraaktiverad virtuell dator:

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

### <a name="create-an-ultra-disk-using-powershell"></a>Skapa en ultradisk med PowerShell

Nu när du har en virtuell dator som kan använda ultra diskar, kan du skapa och bifoga en ultra disk till den:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Ansluta en ultradisk till en virtuell dator med PowerShell

Alternativt, om din befintliga virtuella dator är i en region / tillgänglighet zon som kan använda ultra diskar, kan du använda ultra diskar utan att behöva skapa en ny virtuell dator.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Justera prestanda för en ultradisk med PowerShell

Ultra diskar har en unik funktion som gör att du kan justera deras prestanda, är följande kommando ett exempel som justerar prestanda utan att behöva koppla bort disken:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Nästa steg

Om du vill prova den nya åtkomsten för begäran om disktyp [med den här undersökningen](https://aka.ms/UltraDiskSignup).
