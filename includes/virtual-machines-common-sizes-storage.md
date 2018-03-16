---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: c2209a06921ffd6a8efb6fc38dacfa88fc87fa05
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
Lagring optimerade VM-storlekar erbjuder ett högt genomflöde och -i/o och lämpar sig för Stordata, SQL och NoSQL-databaser. Den här artikeln innehåller information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.  

## <a name="ls-series"></a>Ls-serien

ACU: 180–240
 
| Storlek          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max temp genomflödet: IOPS / Mbit/s | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Det maximala diskgenomflödet för virtuella datorer i Ls-serien kan begränsas av de anslutna diskarnas antal, storlek och striping. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md). 

<sup>1</sup> instans är isolerad till maskinvara som är dedikerad till en kund.

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Ls-serien datadiskar fungerar inte i cachelagrat läge, cacheläge värden måste anges till **ingen**.
- Om du vill få bästa prestanda för dina virtuella datorer bör du begränsa antalet datadiskar till 2 diskar per vCPU.
- **Förväntat nätverksbandbredd** sammanställs maximalt [bandbredd som allokerats per VM typ](../articles/virtual-network/virtual-machine-network-throughput.md) på alla nätverkskort för alla mål. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../articles/virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).