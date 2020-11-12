---
title: Ändra prestanda för Azure Managed disks
description: Lär dig mer om prestanda nivåer för hanterade diskar och lär dig hur du ändrar prestanda nivåer för befintliga hanterade diskar med hjälp av Azure PowerShell-modulen eller Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 923c5970183bd192ac1a2f20fb775d96dcc06865
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540645"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestanda nivåer för Managed disks (för hands version)

Azure-disklagring erbjuder inbyggda burst-funktioner som ger högre prestanda för hantering av kortsiktiga oväntade trafik. Premium-SSD har flexibiliteten att öka disk prestanda utan att öka den faktiska disk storleken. Med den här funktionen kan du matcha arbets Belastningens prestanda behov och minska kostnaderna. 

> [!NOTE]
> Den här funktionen finns för närvarande som en förhandsversion. 

Den här funktionen är idealisk för händelser som tillfälligt kräver en ständigt högre prestanda nivå, t. ex. helgdags köp, prestanda testning eller körning av en tränings miljö. Om du vill hantera dessa händelser kan du använda en högre prestanda nivå så länge du behöver den. Du kan sedan gå tillbaka till den ursprungliga nivån när du inte längre behöver den ytterligare prestandan.

## <a name="how-it-works"></a>Så här fungerar det

När du först distribuerar eller etablerar en disk, anges bas linje prestanda nivån för den disken baserat på den allokerade disk storleken. Du kan använda en högre prestanda nivå för att möta högre efter frågan. När du inte längre behöver den prestanda nivån kan du återgå till den första prestanda nivån för bas linjen.

Dina fakturerings ändringar när nivån ändras. Om du till exempel etablerar en P10-disk (128 GiB), anges din bas linje prestanda nivå som P10 (500 IOPS och 100 Mbit/s). Du debiteras enligt P10-priset. Du kan uppgradera nivån så att den matchar prestandan för P50 (7 500 IOPS och 250 Mbit/s) utan att öka disk storleken. Under uppgraderingen debiteras du enligt P50-priset. När du inte längre behöver högre prestanda kan du gå tillbaka till P10-nivån. Disken kommer återigen att debiteras enligt P10-priset.

| Diskstorlek | Bas linje prestanda nivå | Kan uppgraderas till |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Inget |
| 8 TiB | p60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Inget |

Information om fakturering finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Begränsningar

- Den här funktionen stöds för närvarande endast för Premium-SSD.
- Du måste antingen frigöra den virtuella datorn eller koppla från disken från en virtuell dator som körs innan du kan ändra diskens nivå.
- Användningen av prestanda nivåerna P60, P70 och P80 är begränsad till diskar på 4 096 GiB eller högre.
- En disks prestanda nivå kan bara nedgraderas en gång var 24: e timme.

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Skapa en tom datadisk med en högre nivå än bas linje nivån

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Skapa en OS-disk med en högre nivå än bas linje nivån från en Azure Marketplace-avbildning

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Uppdatera nivån på en disk

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Visa nivån för en disk

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>Nästa steg

Om du behöver ändra storlek på en disk för att kunna utnyttja de högre prestanda nivåerna kan du läsa följande artiklar:

- [Expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI](linux/expand-disks.md)
- [Expandera en hanterad disk som är ansluten till en virtuell Windows-dator](windows/expand-os-disk.md)
