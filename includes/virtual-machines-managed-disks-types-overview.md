---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 31a9da0678f602afcc117e5b2f7927af379da668
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468746"
---
Azure Managed disks erbjuder för närvarande fyra disk typer, och varje typ är riktad mot specifika kund scenarier.

## <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av Ultra disks, Premium solid-state-hårddiskar (SSD), standard SSD och standard hård diskar (HDD) för hanterade diskar för att hjälpa dig att avgöra vad som ska användas.

|   | Ultradisk   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Disktyp   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |I/o-intensiva arbets belastningar, till exempel [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), toppnivå databaser (till exempel SQL, Oracle) och andra transaktions krävande arbets belastningar.   |Produktion och prestandakänsliga arbetsbelastningar   |Webbservrar, företagsprogram med lätt användning och Dev/Test   |Säkerhetskopiering, icke-kritisk, lågfrekvent åtkomst   |
|Maximal diskstorlek   |65 536 gibibyte (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximalt dataflöde   |2 000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximalt IOPS   |160 000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultradisk

Azures ultradiskar tillhandahåller disklagring med ett snabbt dataflöde, hög IOPS och konsekvent snabba svarstider för virtuella Azure IaaS-datorer. Vissa ytterligare fördelar med Ultra disks är möjligheten att dynamiskt ändra diskens prestanda, tillsammans med dina arbets belastningar, utan att behöva starta om dina virtuella datorer (VM). Ultradiskar är lämpliga för dataintensiva arbetsbelastningar som SAP HANA, databaser på toppnivå och transaktionskrävande arbetsbelastningar. Ultradiskar kan endast användas som datadiskar. Vi rekommenderar att du använder Premium-SSD-diskar som operativsystemsdiskar.

### <a name="performance"></a>Prestanda

När du etablerar en Ultra disk kan du oberoende konfigurera kapaciteten och diskens prestanda. Ultra disks har flera fasta storlekar, från 4 GiB upp till 64 TiB och en flexibel prestanda konfigurations modell som gör att du kan konfigurera IOPS och data flöde oberoende av varandra.

Några viktiga funktioner i Ultra disks:

- Disk kapacitet: Ultra disks-kapacitet sträcker sig från 4 GiB upp till 64 TiB.
- Disk IOPS: Ultra disks stöder IOPS-gränser på 300 IOPS/GiB, upp till högst 160 K IOPS per disk. Säkerställ att den valda disk-IOPS är mindre än den virtuella datorns IOPS-gräns för att uppnå den IOPS som du har allokerat. Lägsta IOPS per disk är 2 IOPS/GiB, med en total bas linje som är minst 100 IOPS. Om du till exempel har en 4 GiB Ultra-disk får du minst 100 IOPS i stället för åtta IOPS.
- Disk data flöde: med Ultra disks är data flödes gränsen på en enskild disk 256 KiB/s för varje etablerad IOPS, upp till högst 2000 MBps per disk (där Mbit/s = 10 ^ 6 byte per sekund). Lägsta data flöde per disk är 4KiB/s för varje etablerad IOPS, med en total bas linje som är minst 1 Mbit/s.
- Ultra disks stöder justering av attribut för disk prestanda (IOPS och data flöde) vid körning utan att disken kopplas bort från den virtuella datorn. När en disk prestanda har ändrats på en disk kan det ta upp till en timme innan ändringen träder i kraft. Det finns en gräns för fyra åtgärder för storleks ändring under en 24-timmarsperiod. Det går att ändra storlek på en prestanda för att kunna köras på grund av brist på prestanda bandbredds kapacitet.

### <a name="disk-size"></a>Diskstorlek

|Diskstorlek (GiB)  |IOPS-hölje  |Data flödes gräns (Mbit/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2,000         |
|64     |19 200         |2,000         |
|128     |38 400         |2,000         |
|256     |76 800         |2,000         |
|512     |80,000         |2,000         |
|1 024-65536 (storlekar i det här intervallet ökar i steg om 1 TiB)     |160 000         |2,000         |

### <a name="ga-scope-and-limitations"></a>Allmän omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]