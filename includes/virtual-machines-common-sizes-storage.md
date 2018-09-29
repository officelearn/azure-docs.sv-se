---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 961f82cd4970abfdd11a30b2847a14f8ff1880b0
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454565"
---
Lagring optimerad VM-storlekar ger högt diskgenomflöde och I/O och är perfekt för Stordata, SQL och NoSQL-databaser. Den här artikeln innehåller information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.  Virtuella datorer i ls-serien är idealiska för program som kräver låg latens, högt dataflöde och stort lokalt diskutrymme. 

Exemplen omfattar NoSQL-databaser som Cassandra, MongoDB, Cloudera och Redis, informationslagerhantering och stora transaktionsdatabaser.

> [!NOTE]
> Ls-serien har optimerats för användning av den temporära disken som är kopplade till den Virtuella datorn i stället för användning av beständiga datadiskar. Högt dataflöde och IOPS för den temporära disken gör du Ls-serien perfekt för NoSQL, till exempel Apache Cassandra och MongoDB som replikerar data mellan flera virtuella datorer att uppnå beständighet i händelse av fel på en enskild virtuell dator. Ls-serien stöder inte skapandet av en lokal cache för att öka IOPS kan uppnås genom beständiga datadiskar.

## <a name="ls-series"></a>Ls-serien

ACU: 180–240

Premium Storage: stöds

Premium Storage cachelagring: Stöds inte
 
| Storlek          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5,000 / 125        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10,000 / 250       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20,000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Det maximala diskgenomflödet som är möjligt med virtuella datorer i Ls-serien kan vara begränsas av antal, storlek och striping av alla anslutna diskar. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Om du vill ha bästa möjliga prestanda för dina virtuella datorer, bör du begränsa antalet datadiskar till 2 diskar per virtuell processor.
- **Förväntat nätverksbandbredd** sammanställs max [bandbredden som allokeras per VM-typ](../articles/virtual-network/virtual-machine-network-throughput.md) på alla nätverkskort, för alla mål. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../articles/virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).
