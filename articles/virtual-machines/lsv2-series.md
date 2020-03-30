---
title: Lsv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Lsv2-serien.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164040"
---
# <a name="lsv2-series"></a>Lsv2-serien

Lsv2-serien har hög genomströmning, låg latens, direkt mappad lokal NVMe-lagring som körs på [AMD EPYC<sup>TM</sup> 7551-processorn](https://www.amd.com/en/products/epyc-7000-series) med en all core boost på 2,55 GHz och en maximal boost på 3,0 GHz. Virtuella datorer i Lsv2-serien finns i storlekar från 8 till 80 vCPU i en samtidig konfiguration med flera trådar.  Det finns 8 GiB minne per vCPU, och en 1.92TB NVMe SSD M.2 enhet per 8 vCPU, med upp till 19.2TB (10x1.92TB) tillgänglig på L80s v2.

> [!NOTE]
> Virtuella datorer i Lsv2-serien är optimerade för att använda den lokala disken på noden som är ansluten direkt till den virtuella datorn i stället för att använda varaktiga datadiskar. Detta möjliggör större IOPs / dataflöde för dina arbetsbelastningar. Lsv2- och Ls-serien stöder inte skapandet av en lokal cache för att öka de IOPs som kan uppnås av varaktiga datadiskar.
>
> Det höga dataflödet och IOPs för den lokala disken gör virtuella datorer i Lsv2-serien idealiska för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att uppnå persistens i händelse av fel på en enda virtuell dator.
>
> Mer information finns i Optimera prestanda på virtuella datorer i Lsv2-serien för [Windows](../virtual-machines/windows/storage-performance.md) eller [Linux](../virtual-machines/linux/storage-performance.md).  

ACU: 150-175

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Minne (GiB) | Temp disk<sup>1</sup> (GiB) | NVMe Diskar<sup>2</sup> | NVMe Disk dataflöde<sup>3</sup> (Läs IOPS /MBps) | Max oankopplat datadiskdataflöde (IOPs/MBps)<sup>4</sup> | Max datadiskar | Max nätverkskort / Förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1.5M/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2.2M/14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8 / 16000+ |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10x1.92TB | 3.8M/20000 | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Virtuella datorer i Lsv2-serien har en standard-SCSI-baserad temp-resursdisk för filanvändning för operativsystemets växlingsfiler (D: på Windows, /dev/sdb på Linux). Den här disken ger 80 GiB lagringsutrymme, 4 000 IOPS och 80 MBps-överföringshastighet för varje 8 vCPUs (t.ex. Standard_L80s_v2 ger 800 GiB vid 40 000 IOPS- och 800 MBPS). Detta säkerställer att NVMe-enheterna kan vara helt dedikerade till programanvändning. Den här disken är efemära och alla data går förlorade vid stopp/deallocate.

<sup>2</sup> Lokala NVMe Diskar är efemära, data kommer att gå förlorade på dessa diskar om du stoppar / deallocate din virtuella dator.

<sup>3</sup> Hyper-V NVMe Direct-teknik ger oåtstrott åtkomst till lokala NVMe-enheter som är placerade på ett säkert sätt i gäst-VM-utrymmet.  För att uppnå maximal prestanda krävs att du använder antingen den senaste WS2019-versionen eller Ubuntu 18.04 eller 16.04 från Azure Marketplace.  Skrivprestanda varierar beroende på IO-storlek, enhetsbelastning och kapacitetsutnyttjande.

<sup>Virtuella</sup> datorer i Lsv2-serien tillhandahåller inte värdcachen för datadisken eftersom den inte gynnar Lsv2-arbetsbelastningarna.  Lsv2 virtuella datorer kan dock ta emot Azures ephemeral VM OS-diskalternativ (upp till 30 GiB).

<sup>5</sup> virtuella datorer med mer än 64 virtuella processorer kräver ett av dessa gästoperativsystem som stöds:

- Windows Server 2016 eller senare
- Ubuntu 16.04 LTS eller senare, med Azure-trimmad kärna (4.15-kernel eller senare)
- SLES 12 SP2 eller senare
- RHEL- eller CentOS version 6.7 till 6.10, med LIS-paket 4.3.1 (eller senare) installerat
- RHEL- eller CentOS version 7.3, med LIS-paket 4.2.1 (eller senare) installerat
- RHEL eller CentOS version 7.6 eller senare
- Oracle Linux med UEK4 eller senare
- Debian 9 med backportskärnan, Debian 10 eller senare
- CoreOS med en 4.14-kärna eller senare

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Om du vill få bästa prestanda för dina virtuella datorer bör du begränsa antalet datadiskar till 2 diskar per vCPU.
- **Förväntad nätverksbandbredd** är den maximala aggregerade [bandbredden som allokeras per vm-typ](../virtual-network/virtual-machine-network-throughput.md) för alla nätverkskort för alla destinationer. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
