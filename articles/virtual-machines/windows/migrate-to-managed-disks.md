---
title: Migrera virtuella Azure-datorer till Managed Disks | Microsoft Docs
description: Migrera virtuella Azure-datorer skapas med ohanterade diskar i storage-konton för att använda Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: b87e27ae914a01f03ce78eafe5792433d18e417f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193717"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till Managed Disks i Azure

Azure Managed Disks förenklar din lagringshantering genom att ta bort behovet av att hantera storage-konton separat.  Du kan också migrera dina befintliga virtuella Azure-datorer till Managed Disks för att dra nytta av bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det innebär att diskar på olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritisk fel. Den placerar automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika lagringsskalenheter (stämplar) vilket begränsar effekten av enskild lagringsskalningsenhetsfel bero på att maskinvara och programvarufel.
Utifrån dina behov kan välja du mellan två typer av alternativ för lagring:

- [Premium Managed Disks](premium-storage.md) är Solid tillstånd enhet (SSD) baserade lagringsmedia som levererar högpresterande disksupport med låg fördröjning för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Du kan dra nytta av hastigheten och prestandan för dessa diskar genom att migrera till Premium Managed Disks.

- [Standard Managed Disks](standard-storage.md) använder hårddisken (HDD)-baserade lagringsmedia och passar bäst till utveckling/testning och andra arbetsbelastningar med lågfrekvent dataåtkomst som är mindre känsliga för varierande prestanda.

Du kan migrera till Managed Disks i följande scenarier:

| Migrera...                                            | Länk till dokumentation                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till managed disks   | [Konvertera virtuella datorer om du vill använda hanterade diskar](convert-unmanaged-to-managed-disks.md) |
| En enskild virtuell dator från klassisk till Resource Manager på hanterade diskar     | [Skapa en virtuell dator från en klassisk virtuell Hårddisk](create-vm-specialized-portal.md)  | 
| Alla virtuella datorer i ett virtuellt nätverk från klassisk till Resource Manager på hanterade diskar     | [Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och sedan [konvertera en virtuell dator från ohanterade diskar till managed disks](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planera för konvertering till Managed Disks

Det här avsnittet hjälper dig att fatta det bästa beslutet på virtuell dator och disk-typer.


## <a name="location"></a>Plats

Välj en plats där Azure Managed Disks är tillgängliga. Om du flyttar till Premium Managed Disks måste också kontrollera att Premium storage är tillgängligt i regionen där du planerar att flytta till. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

## <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium Managed Disks kan behöva du uppdatera storleken på den virtuella datorn till Premium Storage kan storlek tillgängliga i den region där VM finns. Granska de storlekar som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaperna för virtuella datorer med Premium Storage och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Se till att det finns tillräckligt mycket bandbredd på den virtuella datorn att driva trafiken disk.

## <a name="disk-sizes"></a>Diskstorlekar

**Premium Managed Disks**

Det finns sju typer av premium-hanterade diskar som kan användas med den virtuella datorn och alla har specifika IOPs och dataflöde gränser. Ta hänsyn till dessa gränser när välja Premium-disktyp för din virtuella dator baserat på dina behov för ditt program när det gäller kapacitet, prestanda, skalbarhet och högsta läses in.

| Typen för Premium-diskar  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4 095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100  |2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund |150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

**Hanterade standarddiskar**

Det finns sju typer av hanterade standarddiskar som kan användas med den virtuella datorn. Var och en av dem har kapacitet för olika men har samma IOPS och dataflöde gränser. Välj typ av Standard Managed disks baserat på programmets kapacitetsbehov.

| Disk av standardtyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1 024 GB (1 TB)   | 2048 GB (2TB)    | 4 095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 

## <a name="disk-caching-policy"></a>Disk-principen för cachelagring

**Premium Managed Disks**

Som standard disken Cachelagringsprincip är *skrivskyddad* för alla Premium datadiskar, och *skrivskyddad* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. Inaktivera diskcachelagring så att du kan få bättre prestanda för hög eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

## <a name="pricing"></a>Prissättning

Granska den [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Priser för Premium Managed Disks är samma som Premium Unmanaged Disks. Men priserna för Standard Managed Disks skiljer sig från vanliga ohanterade diskar.



## <a name="next-steps"></a>Nästa steg

- Läs mer om [Managed Disks](managed-disks-overview.md)
