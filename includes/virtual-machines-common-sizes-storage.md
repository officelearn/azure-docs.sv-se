---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 765ee3c737adbe1da89b9e908d0e22e44d0f29ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748853"
---
Storage-optimerade VM-storlekar erbjuder hög disk data flöde och IO och är idealiska för Big data, SQL, NoSQL-databaser, data lager hantering och stora transaktions databaser.  Exempel är Cassandra, MongoDB, Cloudera och Redis. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lokalt lagrings data flöde och nätverks bandbredd för varje optimerad storlek.

Lsv2-serien har högt data flöde, kort svars tid, direkt mappad lokal NVMe-lagring som körs på [AMD EPYC &trade; 7551-processorn](https://www.amd.com/en/products/epyc-7000-series) med all Core-höjning på 2.55 GHz och en maximal förstärkning på 3,0 GHz. Virtuella datorer i Lsv2-serien finns i storlekar mellan 8 och 80 vCPU:er i en konfiguration med simultan flertrådskörning.  Det finns 8 GiB minne per vCPU och en NVMe SSD M.2-enhet på 1,92 TB per 8 vCPU:er, med upp till 19,2 TB (10 x 1,92 TB) tillgängligt på L80s v2.

> [!NOTE]
> De virtuella datorerna i Lsv2-serien är optimerade för att använda den lokala disken på noden som är ansluten direkt till den virtuella datorn i stället för att använda varaktiga data diskar Detta möjliggör större IOPs/data flöde för dina arbets belastningar. Lsv2 och LS-serien stöder inte att en lokal cache skapas för att öka IOPs som kan nås av varaktiga data diskar.
>
> Med det höga genom strömningen och IOPs av den lokala disken blir de virtuella datorerna i Lsv2 och LS-serien idealiska för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att få beständighet i händelse av att en enskild virtuell dator Miss lyckas.
>
> Läs mer i [optimera prestanda på virtuella datorer i Lsv2-serien](../articles/virtual-machines/linux/storage-performance.md).  


## <a name="lsv2-series"></a>Lsv2-serien

ACU: 150-175

Premium Storage: stöds

Premium Storage caching: stöds inte

| Storlek          | vCPU | Minne (GiB) | Temporär disk<sup>1</sup> (GIB) | NVMe-diskar<sup>2</sup> | NVMe-disk data flöde<sup>3</sup> (läsa IOPS/Mbit/s) | Högsta data flöde för ej cachelagrade data diskar (IOPs/Mbit/s)<sup>4</sup> | Maximalt antal data diskar | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200  |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400  |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32 | 8 / 12800 |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2,2 m/14000   | 48000/960  | 32 | 8/16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2.9 m/16000   | 64000/1280 | 32 | 8/16000 + |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10X 1.92 TB   | 3.8 m/20000   | 80000/1400 | 32 | 8/16000 + |

<sup>1</sup> virtuella datorer i Lsv2-serien har en SCSI-baserad temporär resurs disk för växlings-/växlings fil för operativ system (D: i Windows,/dev/SDB på Linux). Den här disken ger 80 GiB lagring, 4 000 IOPS och 80 MBps-överförings takt för varje 8-virtuella processorer (t. ex. Standard_L80s_v2 tillhandahåller 800 GiB vid 40 000 IOPS och 800 MBIT/s). Detta säkerställer att NVMe-enheter kan användas fullt ut för användning av program. Den här disken är tillfällig och alla data går förlorade vid stopp/frigörning.

<sup>2</sup> lokala NVMe-diskar är tillfälliga. data kommer att gå förlorade på diskarna om du stoppar/frigör den virtuella datorn.

<sup>3</sup> Hyper-V NVMe Direct-teknik ger obegränsad åtkomst till lokala NVMe-enheter som mappats säkert till det virtuella gäst dator utrymmet.  För att uppnå högsta prestanda måste du använda antingen den senaste WS2019-versionen eller Ubuntu 18,04 eller 16,04 från Azure Marketplace.  Skriv prestanda varierar beroende på IO-storlek, enhets belastning och kapacitets användning.

<sup>4</sup> virtuella datorer i Lsv2-serien tillhandahåller inte värd-cache för datadisk eftersom den inte drar nytta av Lsv2-arbetsbelastningar.  Virtuella Lsv2-datorer kan dock hantera Azures alternativ för den tillfälliga virtuella datorns OS-disk (upp till 30 GiB).

<sup>5</sup> virtuella datorer med mer än 64 virtuella processorer kräver ett av de gäst operativ system som stöds:
- Windows Server 2016 eller senare
- Ubuntu 16,04 LTS eller senare med Azures justerade kernel (4,15 kernel eller senare)
- SLES 12 SP2 eller senare
- RHEL eller CentOS version 6,7 till 6,10, med Microsoft-medföljande LIS-paket 4.3.1 (eller senare) installerat
- RHEL eller CentOS version 7,3, med Microsoft-tillhandahållen LIS-paket 4.2.1 (eller senare) installerat
- RHEL eller CentOS version 7,6 eller senare
- Oracle Linux med UEK4 eller senare
- Debian 9 med backports-kärnan, Debian 10 eller senare
- Core-attribut med en 4,14-kernel eller senare


## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Om du vill få bästa möjliga prestanda för dina virtuella datorer bör du begränsa antalet data diskar till 2 diskar per vCPU.
- **Förväntad nätverks bandbredd** är den högsta sammanlagda [allokerade bandbredden per VM-typ](../articles/virtual-network/virtual-machine-network-throughput.md) i alla nätverkskort, för alla mål. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../articles/virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).
