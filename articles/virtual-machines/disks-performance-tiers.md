---
title: Förbättra prestanda för Azure Managed disk
description: Lär dig mer om prestanda nivåer för hanterade diskar, samt hur du uppgraderar prestanda nivåer för dina hanterade diskar.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3d6b243ab517f3663f779d01569acf3d46ad8411
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328130"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestanda nivåer för Managed disks (för hands version)

Azure-disklagring erbjuder för närvarande inbyggda funktioner för burst-överföring för att uppnå högre prestanda för att hantera kortsiktig oväntad trafik. Premium-SSD har flexibiliteten att öka disk prestanda utan att öka den faktiska disk storleken, vilket gör att du kan matcha arbets Belastningens prestanda behov och minska kostnaderna. funktionen är för närvarande en för hands version. Detta är idealiskt för händelser som tillfälligt kräver en konsekvent högre prestanda nivå, till exempel jul handel, prestandatester, prestanda testning eller körning av en utbildnings miljö. Om du vill hantera dessa händelser kan du välja en högre prestanda nivå så länge som behövs, och återgå till den ursprungliga nivån när den ytterligare prestandan inte längre behövs.

## <a name="how-it-works"></a>Så här fungerar det

När du först distribuerar eller etablerar en disk, anges bas linje prestanda nivån för den disken baserat på den allokerade disk storleken. Du kan välja en högre prestanda nivå för att möta högre efter frågan och när prestandan inte längre krävs kan du gå tillbaka till den första bas linje prestanda nivån.

Dina fakturerings ändringar när nivån ändras. Om du till exempel etablerar en P10-disk (128 GiB), anges din bas linje prestanda nivå som P10 (500 IOPS och 100 MB/s) och du debiteras enligt P10-priset. Du kan uppdatera nivån så att den matchar prestandan för P50 (7500 IOPS och 250 MB/s) utan att öka disk storleken, då du debiteras enligt P50-priset. När den högre prestandan inte längre behövs kan du gå tillbaka till P10-nivån så kommer disken att faktureras igen enligt P10-priset.

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
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Inget |
| 8 TiB | p60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Inget |

Information om fakturering finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Begränsningar

- Stöds för närvarande endast för Premium-SSD.
- Diskarna måste vara frånkopplade från en virtuell dator som körs innan du ändrar nivåer.
- Användningen av prestanda nivåerna P60, P70 och P80 är begränsad till diskar på 4096 GiB eller mer.
- Prestanda nivån disks kan bara ändras en gång var 24: e timme.

## <a name="regional-availability"></a>Regional tillgänglighet

Att justera prestanda nivån för en hanterad disk är för närvarande bara tillgänglig för Premium-SSD i följande regioner:

- USA, västra centrala 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Skapa en tom datadisk med en högre nivå än bas linje nivån

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
     
## <a name="update-the-tier-of-a-disk"></a>Uppdatera nivån på en disk

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Visa nivån för en disk

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Nästa steg

Om du måste ändra storlek på en disk för att kunna dra nytta av de större prestanda nivåerna, Se våra artiklar om ämnet:

- [Expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI](linux/expand-disks.md)
- [Expandera en hanterad disk som är ansluten till en virtuell Windows-dator](windows/expand-os-disk.md)
