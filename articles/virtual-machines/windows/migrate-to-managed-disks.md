---
title: Migrera virtuella Azure-datorer till hanterade diskar | Microsoft Docs
description: Migrera Azure virtuella datorer som skapats med hjälp av ohanterade diskar i storage-konton du använda hanterade diskar.
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
ms.openlocfilehash: d280ad1180949167bb8ebfc6b21521736db0f55d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777175"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrera virtuella Azure-datorer till hanterade diskar i Azure

Azure-hanterade diskar förenklas hanteringen lagring genom att ta bort behovet av att hantera separat storage-konton.  Du kan också migrera dina befintliga virtuella Azure-datorer till hanterade diskar för att dra nytta av bättre tillförlitlighet för virtuella datorer i en Tillgänglighetsuppsättning. Det garanterar att diskar för olika virtuella datorer i en Tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika enda åtkomstpunkt för fel. Den placeras automatiskt diskar på olika virtuella datorer i en Tillgänglighetsuppsättning i olika skalningsenheter (stämplar) som begränsar effekten av enskild skala enhet lagringsfel orsakade på grund av maskinvara och programvarufel.
Utifrån dina behov kan välja du mellan två typer av lagringsalternativ:

- [Hanterade Premiumdiskar](premium-storage.md) är fylld tillstånd enhet (SSD) baserade lagringsmedia som ger hög prestanda, låg latens diskstöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar. Du kan dra nytta av hastighet och prestanda för dessa diskar genom att migrera till Premium hanterade diskar.

- [Hanterade standarddiskar](standard-storage.md) använda hårddisken (HDD) baserade lagringsmedia och passar bäst för utveckling och testning och andra regelbunden åtkomst arbetsbelastningar som är mindre känsliga för prestanda variationen.

Du kan migrera till hanterade diskar i följande scenarier:

| Migrera...                                            | Länk till dokumentation                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konvertera fristående virtuella datorer och virtuella datorer i en tillgänglighetsuppsättning till hanterade diskar   | [Konvertera virtuella datorer om du vill använda hanterade diskar](convert-unmanaged-to-managed-disks.md) |
| En enda virtuell dator från klassisk till Resource Manager på hanterade diskar     | [Migrera en enda virtuell dator](migrate-single-classic-to-resource-manager.md)  | 
| Alla virtuella datorer i ett virtuellt nätverk från klassisk till Resource Manager på hanterade diskar     | [Migrera IaaS-resurser från klassisk till Resource Manager](migration-classic-resource-manager-ps.md) och sedan [konvertera en virtuell dator från ohanterade diskar till hanterade diskar](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planera för konvertering till hanterade diskar

Det här avsnittet hjälper dig att göra det bästa på VM- och diskresurser typer.


## <a name="location"></a>Plats

Välj en plats där Azure hanterade diskar är tillgängliga. Om du flyttar till hanterade Premiumdiskar också se till att Premium-lagring är tillgänglig i den region där du planerar att flytta till. Se [Azure-tjänster efter Region](https://azure.microsoft.com/regions/#services) uppdaterad information om tillgängliga platser.

## <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium hanterade diskar som du behöver uppdatera storleken på den virtuella datorn till Premium-lagring kan storlek i den region där VM finns. Granska storlek på Virtuella datorer som är Premium-lagring som är kompatibla. Specifikationer för Azure VM-storleken anges i [storlekar för virtuella datorer](sizes.md).
Granska prestandaegenskaper för virtuella datorer som fungerar med Premium-lagring och välja den lämpligaste VM-storlek som bäst passar din arbetsbelastning. Kontrollera att det finns tillräckligt med bandbredd på den virtuella datorn för att ge disk-trafik.

## <a name="disk-sizes"></a>Diskstorlekar

**Premium hanterade diskar**

Det finns sju typer av hanterade premiumdiskar som kan användas med den virtuella datorn var och en har särskilda IOPs och genomströmning gränser. Ta hänsyn till dessa begränsningar när du väljer Premium disktyp för den virtuella datorn baserat på dina behov av ditt program vad gäller kapacitet, prestanda, skalbarhet och belastning läser in.

| Premium diskar typ  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1 024 GB (1 TB)    | 2 048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100  |2 300              | 5000              | 7500              | 7500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund |150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

**Hanterade standarddiskar**

Det finns sju typer av hanterade standarddiskar som kan användas med den virtuella datorn. Var och en av dem har olika kapacitet men samma IOPS och genomströmning gränser. Välj typ av Standard hanterade diskar baserat på kapacitetsbehov för programmet.

| Disk av standardtyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1 024 GB (1 TB)   | 2 048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 

## <a name="disk-caching-policy"></a>Princip för cachelagring av disk

**Premium hanterade diskar**

Princip för cachelagring av disk är som standard *skrivskyddad* för alla Premium datadiskar, och *Read-Write* för Premium operativsystemets disk som är kopplade till den virtuella datorn. Den här inställningen rekommenderas för att uppnå optimal prestanda för ditt program IOs. Inaktivera cachelagring på disk så att du kan få bättre prestanda för programmet för skrivintensiv eller lässkyddad datadiskar (till exempel loggfiler för SQL Server).

## <a name="pricing"></a>Prissättning

Granska de [priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/). Priser för hanterade Premiumdiskar är samma som ohanterad Premiumdiskar. Men priser för hanterade standarddiskar skiljer sig från ohanterade standarddiskar.



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterade diskar](managed-disks-overview.md)
