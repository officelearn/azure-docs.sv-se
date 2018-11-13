---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75b3934a7329b4e83a0f36f79bbc8365eaf8a086
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572775"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Standard SSD Managed Disks för virtuella Azure-datorbelastningar

Azure Standard Solid tillstånd-hårddiskar (SSD) Managed Disks är ett alternativ för kostnadseffektiv lagring som optimerats för arbetsbelastningar som behöver konsekvent prestanda på lägre nivåer av IOPS. Standard SSD erbjuder en bra enklare upplevelse för dem som vill flytta till molnet, särskilt om det uppstår problem med variansen för arbetsbelastningar som körs på dina HDD-lösningar lokalt. Standard SSD leverera bättre tillgänglighet, konsekvens, tillförlitlighet och svarstid jämfört med HDD-diskar och är lämpliga för webbservrar, programservrar för låg IOPS, används företagsprogram och arbetsbelastningar för utveckling och testning.

## <a name="standard-ssd-features"></a>Standard SSD-funktioner

**Hanterade diskar**: Standard SSD-enheter är endast tillgängliga som Managed Disks. Ohanterade diskar och Sidblobar stöds inte på Standard SSD. När du skapar den hanterade disken måste du ange typ av disk som Standard SSD och ange storlek på disk och Azure skapar och hanterar disken åt dig.
Standard SSD-enheter stöder alla åtgärder för klassisk distribution modellen erbjuds av Managed Disks. Exempelvis kan du skapa, kopiera eller ögonblicksbild Standard SSD Managed Disks på samma sätt som du gör med Managed Disks.

**Virtuella datorer**: Standard SSD-enheter kan användas med alla Azure virtuella datorer, inklusive de typer av virtuella datorer som inte har stöd för Premium-diskar. Exempel: Om du använder en virtuell dator i A-serien, eller virtuell N-serien eller DS-serien, eller alla andra Virtuella Azure-serien, du kan använda Standard SSD-enheter med den virtuella datorn. Med introduktionen av Standard SSD möjliggör vi ett brett utbud av arbetsbelastningar som tidigare använt HDD-baserade diskar att övergå till SSD-baserade diskar och Upplev konsekvent prestanda, högre tillgänglighet, bättre svarstid och en övergripande bättre upplevelse som medföljer SSD-enheter.

**Extremt tillförlitliga och tillgängliga**: Standard SSD bygger på samma Azure-diskar-plattformen, som har levererat konsekvent hög tillgänglighet och tillförlitlighet för diskar. Azure-diskar som är utformade för 99,999 procents tillgänglighet. Standard SSD erbjuds även lokalt Redundant lagring (LRS) som alla hanterade diskar. Med LRS, plattformen underhåller flera kopior av data för alla diskar och konsekvent har levererat företagsklass hållbarhet för IaaS-diskar, med en branschledande noll procent årlig Felfrekvens.

**Ögonblicksbilder**: som alla Managed Disks, Standard SSD-enheter stöder även att ögonblicksbilder skapas. Typ av ögonblicksbild kan vara antingen Standard (HDD) eller Premium (SSD). För att spara kostnader rekommenderar vi typ av ögonblicksbild av Standard (HDD) för alla Azure-disktyper. Det beror på att när du skapar en hanterad disk från en ögonblicksbild, är du alltid kan du välja en högre nivå, till exempel Standard SSD- eller Premium SSD.

## <a name="scalability-and-performance-targets"></a>Mål för skalbarhet och prestanda

I följande tabell innehåller diskstorlekar som för närvarande erbjuds för Standard SSD. Storlekar som är markerad med en asterisk finns för närvarande i förhandsversion.

|Standard SSD-disktyp  |Diskstorlek  |IOPS per Disk  |Dataflöde per disk  |
|---------|---------|---------|---------|
|E10     |128 GiB         |Upp till 500         |Upp till 60 MiB per sekund         |
|E15     |256 GB         |Upp till 500         |Upp till 60 MiB per sekund         |
|E20     |512 GiB         |Upp till 500         |Upp till 60 MiB per sekund         |
|E30     |1 024 giB       |Upp till 500         |Upp till 60 MiB per sekund         |
|E40     |2 048 giB       |Upp till 500         |Upp till 60 MiB per sekund         |
|E50     |4095 giB       |Upp till 500         |Upp till 60 MiB per sekund         |
|E60 *     |8 192 giB       |Upp till 1 300       |Upp till 300 MiB per sekund        |
|E70 *    |16 384 giB      |Upp till 2 000       |Upp till 500 MiB per sekund        |
|E80 *    |32 767 giB      |Upp till 2 000       |Upp till 500 MiB per sekund        |

Standard SSD-enheter är utformade för att tillhandahålla ensiffrig svarstid för de flesta i/o-åtgärder och leverera IOPS och dataflöde upp till de gränser som beskrivs i ovanstående tabellen 99% av tiden. Faktiska IOPS och dataflöden varierar ibland beroende på trafikmönster. Standard SSD-enheter ger mer konsekventa prestanda än HDD-diskar med kortare svarstider.

Premium SSD å andra sidan, prestanda bättre än Standard SSD-diskar med låg latens, högt IOPS/dataflöde och ännu bättre konsekvens med etablerad diskprestanda. Premium SSD är den rekommenderade disktypen för kritiska produktionsarbetsbelastningar. Om arbetsbelastningen kräver stöd för diskar med höga prestanda och låg latens, bör du överväga att använda Premium Storage.

Som Premium SSD använda Standard SSD-enheter även i/o-enhet storleken på 256 KiB. Om de data som överförs är mindre än 256 KiB, betraktas den 1 i/o-enhet. Större i/o-storlekar räknas som flera I/o med storleken 256 KiB. Till exempel räknas en 1 100 KiB-i/o som fem i/o-enheter.

## <a name="pricing-and-billing"></a>Priser och fakturering

När du använder Standard SSD-enheter, gäller följande för debitering:

- Hanterade diskens storlek
- Ögonblicksbilder
- Utgående dataöverföringar
- Transaktioner

**Hanterade diskstorlek**: Managed disks debiteras storleken du fått. Azure mappar den etablerade storleken (avrundad uppåt) till närmaste diskstorlekserbjudande. Se tabellen i skalbarhets- och prestandamål ovan för information om diskstorlekar som erbjuds. Varje disk som mappar till en etablerad diskstorleken som stöds och debiteras därefter. Till exempel om du har etablerat en 200 GiB Standard SSD den mappas till disk storlek erbjudandet om E15 (256 GB). Debiteringen för etablerade diskar beräknas per timme med hjälp av det månatliga priset för Premium Storage-erbjudandet. Till exempel om du har etablerat en E10 disk och tar bort den efter 20 timmar faktureras du för erbjudandet E10 beräknat på 20 timmar. Det här är oavsett mängden faktiska data som skrivs till disken.

**Ögonblicksbilder**: ögonblicksbilder för Managed Disks debiteras för den kapacitet som används av ögonblicksbilder, på målet och källan, om sådana. Mer information om ögonblicksbilder finns [hanteras diskbilder](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Utgående dataöverföringar**: [utgående dataöverföringar](https://azure.microsoft.com/pricing/details/bandwidth/) (data som går ut från Azures datacenter) debiteras för bandbreddsanvändning.

**Transaktioner**: liknar Standard HDD, transaktioner på Standard SSD: er debiteras. Transaktioner omfattar både läs- och skrivåtgärder på disken. Storlek på i/o används för redovisning transaktionerna på Standard SSD är 256 KiB. Större i/o-storlekar räknas som flera I/o med storleken 256 KiB.

Mer information om priser för virtuella datorer och hanterade diskar finns:

- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar standard SSD-enheter, finns exempel som visar hur du skapar en virtuell dator med flera standard SSD-enheter.

> [!div class="nextstepaction"]
> [Skapa en virtuell dator med flera standard SSD-enheter](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
