---
title: Förbättra prestanda för Azure Managed disk
description: Lär dig mer om prestanda nivåer för hanterade diskar, samt hur du uppgraderar prestanda nivåer för dina hanterade diskar.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974125"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Prestanda nivåer för Managed disks (för hands version)

Azure-disklagring erbjuder för närvarande inbyggda funktioner för burst-överföring för att uppnå högre prestanda för att hantera kortsiktig oväntad trafik. Premium-SSD har flexibiliteten att öka disk prestanda utan att öka den faktiska disk storleken, vilket gör att du kan matcha arbets Belastningens prestanda behov och minska kostnaderna. funktionen är för närvarande en för hands version. Detta är idealiskt för händelser som tillfälligt kräver en konsekvent högre prestanda nivå, till exempel jul handel, prestandatester, prestanda testning eller körning av en utbildnings miljö. Om du vill hantera dessa händelser kan du välja en högre prestanda nivå så länge som behövs, och återgå till den ursprungliga nivån när den ytterligare prestandan inte längre behövs.

## <a name="how-it-works"></a>Så här fungerar det

När du först distribuerar eller etablerar en disk, anges bas linje prestanda nivån för den disken baserat på den allokerade disk storleken. Du kan välja en högre prestanda nivå för att möta högre efter frågan och när prestandan inte längre krävs kan du gå tillbaka till den första bas linje prestanda nivån. Om du till exempel etablerar en P10-disk (128 GiB), anges din bas linje prestanda nivå som P10 (500 IOPS och 100 MB/s). Du kan uppdatera nivån så att den matchar prestandan för P50 (7500 IOPS och 250 MB/s) utan att öka disk storleken och återgå till P10 när den högre prestandan inte längre behövs.

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
| 4 TiB | P50 | Ingen |
| 8 TiB | p60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Ingen |

## <a name="restrictions"></a>Begränsningar

- Stöds för närvarande endast för Premium-SSD.
- Diskarna måste vara frånkopplade från en virtuell dator som körs innan du ändrar nivåer.
- Användningen av prestanda nivåerna P60, P70 och P80 är begränsad till diskar på 4096 GiB eller mer.

## <a name="regional-availability"></a>Regional tillgänglighet

Att justera prestanda nivån för en hanterad disk är för närvarande bara tillgänglig för Premium-SSD i följande regioner:

- USA, västra centrala 
- Östra 2 USA 
- Västeuropa
- Östra Australien 
- Sydöstra Australien 
- Indien, södra

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Skapa/uppdatera en datadisk med en högre nivå än bas linje nivån

1. Skapa en tom datadisk med en högre nivå än bas linje nivån eller uppdatera nivån på en disk som är högre än bas linje nivån med hjälp av exempel mal len [CreateUpdateDataDiskWithTier.jspå](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Bekräfta nivån på disken

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Skapa/uppdatera en OS-disk med en högre nivå än bas linje nivån

1. Skapa en OS-disk från en Marketplace-avbildning eller uppdatera nivån på en operativ system disk som är högre än bas linje nivån med hjälp av exempel mal len [CreateUpdateOSDiskWithTier.jspå](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Bekräfta nivån på disken
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Nästa steg

Om du måste ändra storlek på en disk för att kunna dra nytta av de större prestanda nivåerna, Se våra artiklar om ämnet:

- [Expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI](linux/expand-disks.md)
- [Expandera en hanterad disk som är ansluten till en virtuell Windows-dator](windows/expand-os-disk.md)