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
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262678"
---
Azure-hanterade diskar erbjuder för närvarande fyra disktyper, varje typ är avsedd för specifika kundscenarier.

## <a name="disk-comparison"></a>Diskjämförelse

Följande tabell innehåller en jämförelse av ultradiskar, SSD(Premium Solid State Drives), standard-SSD och vanliga hårddiskar (HDD) för hanterade diskar som hjälper dig att bestämma vad du ska använda.

|   | Ultradisk   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Disktyp   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |IO-intensiva arbetsbelastningar som [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), top tier-databaser (till exempel SQL, Oracle) och andra transaktionstunga arbetsbelastningar.   |Produktion och prestandakänsliga arbetsbelastningar   |Webbservrar, företagsprogram med lätt användning och Dev/Test   |Säkerhetskopiering, icke-kritisk, lågfrekvent åtkomst   |
|Maximal diskstorlek   |65 536 gibibyte (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximalt dataflöde   |2 000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximalt IOPS   |160 000    |20 000   |6 000   |2 000   |

## <a name="ultra-disk"></a>Ultradisk

Azures ultradiskar tillhandahåller disklagring med ett snabbt dataflöde, hög IOPS och konsekvent snabba svarstider för virtuella Azure IaaS-datorer. Några ytterligare fördelar med ultradiskar inkluderar möjligheten att dynamiskt ändra diskens prestanda, tillsammans med dina arbetsbelastningar, utan att behöva starta om virtuella datorer (VM). Ultradiskar är lämpliga för dataintensiva arbetsbelastningar som SAP HANA, databaser på toppnivå och transaktionskrävande arbetsbelastningar. Ultradiskar kan endast användas som datadiskar. Vi rekommenderar att du använder Premium-SSD-diskar som operativsystemsdiskar.

### <a name="performance"></a>Prestanda

När du etablerar en ultradisk kan du självständigt konfigurera diskens kapacitet och prestanda. Ultra diskar finns i flera fasta storlekar, allt från 4 GiB upp till 64 TiB, och har en flexibel prestanda konfiguration modell som låter dig självständigt konfigurera IOPS och dataflöde.

Några viktiga funktioner för ultradiskar är:

- Diskkapacitet: Ultra diskkapacitet varierar från 4 GiB upp till 64 TiB.
- Disk IOPS: Ultra diskar stöder IOPS-gränser på 300 IOPS/GiB, upp till maximalt 160 K IOPS per disk. För att uppnå IOPS som du har etablerat, se till att den valda disk-IOPS är mindre än VM IOPS-gränsen. Den minsta garanterade IOPS per disk är 2 IOPS/GiB, med en total baslinje minst 100 IOPS. Om du till exempel har en 4 GiB-ultradisk har du minst 100 IOPS i stället för åtta IOPS.
- Diskdataflöde: Med ultradiskar är dataflödesgränsen för en enskild disk 256 KiB/s för varje etablerad IOPS, upp till maximalt 2000 MBps per disk (där MBps = 10^6 Byte per sekund). Det minsta garanterade dataflödet per disk är 4KiB/s för varje etablerad IOPS, med ett totalt baslinjeminimum på 1 MBps.
- Ultra diskar stöd justera diskens prestandaattribut (IOPS och dataflöde) vid körning utan att koppla bort disken från den virtuella datorn. När en diskprestandaändringsåtgärd har utfärdats på en disk kan det ta upp till en timme innan ändringen börjar gälla. Det finns en gräns på fyra åtgärder för att ändra storlek på prestanda under ett 24-timmarsfönster. Det är möjligt att en funktion för att ändra storlek på prestanda misslyckas på grund av bristande kapacitet för bandbredd.

### <a name="disk-size"></a>Diskstorlek

|Diskstorlek (GiB)  |IOPS-lock  |Genomströmningslock (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2,400         |600         |
|16     |4 800         |1200         |
|32     |9 600         |2 000         |
|64     |19,200         |2 000         |
|128     |38,400         |2 000         |
|256     |76 800         |2 000         |
|512     |80 000         |2 000         |
|1 024-65 536 (storlekarna i detta intervall ökar i steg om 1 TiB)     |160 000         |2 000         |

### <a name="ga-scope-and-limitations"></a>GA omfattning och begränsningar

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
