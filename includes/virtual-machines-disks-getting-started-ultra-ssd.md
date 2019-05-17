---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 495326c172f900dc8bcff78b0df38f2cb64ed27e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546521"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Aktivera och distribuera Azure ultra SSD: er (förhandsversion)

Azure ultra solid state-hårddiskar (SSD) (förhandsversion) erbjudandet högt dataflöde, hög IOPS och konsekvent låg latens disklagring för Azure IaaS-datorer (VM). Det här nya erbjudandet ger upp på rad prestanda på samma tillgänglighet nivåer som våra befintliga diskar-erbjudanden. En stor fördel med ultra SSD: er är möjligheten att ändra dynamiskt prestandan hos SSD tillsammans med dina arbetsbelastningar utan att behöva starta om dina virtuella datorer. Ultra SSD lämpar sig för dataintensiva arbetsbelastningar som SAP HANA, översta databaser och transaktionen tunga arbetsbelastningar.

För närvarande ultra SSD: er finns i en förhandsversion och du måste [registrera](https://aka.ms/UltraSSDPreviewSignUp) i förhandsgranskningen för att komma åt dem.

## <a name="determine-your-availability-zone"></a>Fastställa din tillgänglighetszon

När du blivit godkänd, måste du fastställa vilka tillgänglighetszon du är i, för att kunna använda ultra SSD. Kör något av följande kommandon för att avgöra vilken zon i östra USA 2 att distribuera ultra disken till:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==UltraSSD_LRS]”`

Svaret ska vara detsamma som i formuläret här nedan, där X är den zon som ska användas för att distribuera i östra USA 2. X kan vara 1, 2 eller 3.

Bevara den **zoner** värde representerar din tillgänglighetszon och du behöver för att distribuera ett ultra SSD.

|Resurstyp  |Namn  |Location  |Zoner  |Begränsning  |Funktion  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|Diskar     |UltraSSD_LRS         |usaöstra2         |X         |         |         |         |

Om inget svar från kommandot kommer registreringen att funktionen är fortfarande väntande eller inte godkänd ännu.

Nu när du vet vilken zon att distribuera till följer du stegen i den här artikeln för att få din första virtuella datorer som distribueras med ultra SSD för distributionen.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Distribuera ett ultra SSD med Azure Resource Manager

Börja med att kontrollera VM-storlek för att distribuera. En del av den här förhandsversionen är stöds endast DsV3 och EsV3 VM-familjer. Referera till den andra tabellen på den här [blogg](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) för ytterligare information om dessa storlekar på Virtuella datorer.

Om du vill skapa en virtuell dator med flera ultra SSD: er, se exemplet [skapa en virtuell dator med flera ultra SSD](https://aka.ms/UltraSSDTemplate).

Om du planerar att använda en egen mall, se till att **apiVersion** för `Microsoft.Compute/virtualMachines` och `Microsoft.Compute/Disks` har angetts som `2018-06-01` (eller senare).

Ange SKU: n från disk **UltraSSD_LRS**, ställa in diskkapacitet, IOPS, tillgänglighetszon och dataflöde i Mbit/s för att skapa ett ultra disk.

När den virtuella datorn har etablerats kan du partitionera och formatera datadiskar och konfigurera dem för dina arbetsbelastningar.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Distribuera ett ultra SSD med CLI

Börja med att kontrollera VM-storlek för att distribuera. En del av den här förhandsversionen är stöds endast DsV3 och EsV3 VM-familjer. Referera till den andra tabellen på den här [blogg](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) för ytterligare information om dessa storlekar på Virtuella datorer.

Om du vill använda ultra SSD: er, måste du skapa en virtuell dator som kan använda ultra SSD: er.

Ersätta eller ange den **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** variabler med dina egna värden. Ange **$zone** till värdet för din tillgänglighetszon som du fick från den [början av den här artikeln](#determine-your-availability-zone). Kör sedan följande CLI-kommando för att skapa ett ultra aktiverad virtuell dator:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Skapa ett ultra SSD med CLI

Nu när du har en virtuell dator som kan använda ultra SSD: er kan du skapa och koppla ultra SSD-disk till den.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Justera prestanda för ett ultra SSD med CLI

Ultra SSD: er erbjuder en unik funktion som gör att du kan justera prestandan, följande kommando visar hur du använder den här funktionen:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Distribuera ett ultra SSD med hjälp av PowerShell

Börja med att kontrollera VM-storlek för att distribuera. En del av den här förhandsversionen är stöds endast DsV3 och EsV3 VM-familjer. Referera till den andra tabellen på den här [blogg](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) för ytterligare information om dessa storlekar på Virtuella datorer.

Om du vill använda ultra SSD: er, måste du skapa en virtuell dator som kan använda ultra SSD: er. Ersätta eller ange den **$resourcegroup** och **$vmName** variabler med dina egna värden. Ange **$zone** till värdet för din tillgänglighetszon som du fick från den [början av den här artikeln](#determine-your-availability-zone). Kör sedan följande [New-AzVm](/powershell/module/az.compute/new-azvm) kommando för att skapa ett ultra aktiverad virtuell dator:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>Skapa ett ultra SSD med hjälp av PowerShell

Nu när du har en virtuell dator som kan använda ultra SSD: er kan du skapa och bifoga ultra SSD-disk till den:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Justera prestanda för ett ultra SSD med hjälp av PowerShell

Ultra SSD: er har en unik funktion som gör att du kan justera prestandan, följande kommando är ett exempel som justerar prestanda utan att behöva koppla från disken:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Nästa steg

Om du vill prova den nya disktypen [begär åtkomst till förhandsversionen med den här undersökningen](https://aka.ms/UltraSSDPreviewSignUp).