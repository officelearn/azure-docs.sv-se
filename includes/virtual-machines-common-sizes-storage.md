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
ms.openlocfilehash: bb88736762219028d58fe70d0ec32309967c95a4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55830676"
---
Lagring optimerad VM-storlekar ger högt diskgenomflöde och I/O och är perfekt för Stordata, SQL, NoSQL-databaser, informationslagerhantering och stora transaktionsdatabaser.  Exempel är Cassandra, MongoDB, Cloudera och Redis. Den här artikeln innehåller information om hur många virtuella processorer, diskar och nätverkskort samt lokal lagring dataflöde och nätverket bandbredden för varje optimerad storlek.

Den Lsv2-serien funktioner med stora dataflöden och låg latens, direkt mappade lokala NVMe-lagring som körs på den [AMD EPYC &trade; 7551 processor](https://www.amd.com/en/products/epyc-7000-series) med en alla kärnor förstärkningen på 2.55 GHz och en max förstärkningen på 3.0 GHz. Virtuella datorer i Lsv2-serien finns i storlekar mellan 8 och 80 vCPU:er i en konfiguration med simultan flertrådskörning.  Det finns 8 GiB minne per vCPU och en NVMe SSD M.2-enhet på 1,92 TB per 8 vCPU:er, med upp till 19,2 TB (10 x 1,92 TB) tillgängligt på L80s v2.

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.

> [!NOTE]
> De virtuella datorerna Lsv2-serien är optimerade för att använda den lokala disken på den nod som är direkt ansluten till den virtuella datorn i stället för att använda beständiga datadiskar.  Detta möjliggör större IOPs / dataflöde för dina arbetsbelastningar.  Lsv2 och Ls-serien stöder inte skapandet av en lokal cache för att öka IOPS kan uppnås genom beständiga datadiskar. Högt dataflöde och IOPS för den lokala disken gör Lsv2 och virtuella datorer i Ls-serien perfekt för NoSQL, till exempel Apache Cassandra och MongoDB som replikerar data mellan flera virtuella datorer att uppnå beständighet i händelse av fel på en enskild virtuell dator. 

## <a name="lsv2-series"></a>Lsv2-serien
ACU: 150-175

Premium-lagring: Stöds

Premium Storage Caching: Stöds inte

| Storlek          | Virtuell processor | Minne (GiB) | Temporär disk<sup>1</sup> (GiB) | NVMe Disks<sup>2</sup> | NVMe diskdataflöde<sup>3</sup> (Läs IOPS / Mbit/s) | Vara värd för cachestorlek<sup>4</sup> | Maximalt antal Datadiskar | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) | 
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92 TB  | 340,000 / 2,000 | Gäller inte | 16 | 2 / 3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92 TB  | 680,000 / 4,500 | Gäller inte | 32 | 4 / 6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92 TB  | 1.4 M / 9 000 DISKBASERADE    | Gäller inte | 32 | 8 / 12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92 TB  | 2.7 M / 18 000   | Gäller inte | 32 | 8 / 25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.4 M / 22,000   | Gäller inte | 32 | 8 / 32,000 |
 
<sup>1</sup> Lsv2-serien virtuella datorer har standard SCSI-baserat temp resursdisk för OS sidindelning/swap-fil (D: på Windows, /dev/sdb på Linux). Den här disken innehåller 80 GiB lagringsutrymme, 4 000 IOPS och 80 Mbit/s överföringshastighet för varje 8 virtuella processorer (t.ex. Standard_L80s_v2 ger 800 GiB på 40 000 IOPS och 800 Mbit/s). Detta säkerställer att NVMe-enheter kan vara helt reserverad för användningen av. Den här disken är tillfälliga och alla data på Stoppa/Frigör förlorade.

<sup>2</sup> lokala NVMe-diskar är tillfälliga, data går förlorade på diskarna om du stoppa/frigöra den virtuella datorn.

<sup>3</sup> Hyper-V NVMe Direct-teknik ger obegränsad åtkomst till lokala NVMe-enheter på ett säkert sätt mappas till gäst VM utrymme.  För att uppnå maximal prestanda krävs med den senaste versionen av WS2019 eller Ubuntu 18.04 eller 16.04 från Azure Marketplace.  Skrivprestanda varierar beroende på i/o-storlek, enhet belastning och kapacitetsanvändning.

<sup>4</sup> Lsv2-serien virtuella datorer innehåller värden för datadisk som inte nytta av Lsv2-arbetsbelastningar.  Lsv2 virtuella datorer kan dock innehålla alternativet Azures tillfälliga VM OS-disk (upp till 30 GiB). 



## <a name="ls-series"></a>Ls-serien
ACU: 180-240

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds inte
 
| Storlek          | Virtuell processor | Minne (GiB) | Temporär lagring (GiB) | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring (IOPS / Mbit/s) | Maximalt icke cachelagrat diskgenomflöde (IOPS / Mbit/s) | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 
 

Det maximala diskgenomflödet som är möjligt med virtuella datorer i Ls-serien kan vara begränsas av antal, storlek och striping av alla anslutna diskar. Mer information finns i [Premium Storage: Lagring med höga prestanda för Azure-datorbelastningar](../articles/virtual-machines/windows/premium-storage.md).

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Om du vill ha bästa möjliga prestanda för dina virtuella datorer, bör du begränsa antalet datadiskar till 2 diskar per virtuell processor.
- **Förväntat nätverksbandbredd** sammanställs max [bandbredden som allokeras per VM-typ](../articles/virtual-network/virtual-machine-network-throughput.md) på alla nätverkskort, för alla mål. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../articles/virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).
