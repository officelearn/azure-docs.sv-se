---
title: Översikt över virtuella datorer i HC-serien – Azure Virtual Machines | Microsoft Docs
description: Läs mer om för hands versions stöd för VM-storleken för HC-serien i Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 7110f3417937b623260983a9d94e9e6834fc8fc9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077378"
---
# <a name="hc-series-virtual-machine-overview"></a>Översikt över virtuell dator i HC-serien

Att maximera HPC-programmets prestanda på Intel Xeon-skalbara processorer kräver en genomtänkt metod för att bearbeta placeringen på den nya arkitekturen. Här beskriver vi vår implementering av IT på virtuella datorer i Azure HC-serien för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän. På samma sätt kommer vi att använda termen "pCore" för att referera till fysiska processor kärnor och "vCore" för att referera till virtualiserade processor kärnor.

Fysiskt är en HC-Server 2 * 24 kärnor Intel Xeon platina 8168-processorer för totalt 48 fysiska kärnor. Varje CPU är en enskild pNUMA-domän och har enhetlig åtkomst till sex kanaler i DRAM. Intel Xeon platina-processorer har en 4x större L2-cache än i tidigare generationer (256 KB/Core-> 1 MB/Core), samtidigt som L3-cachen minskas jämfört med tidigare Intel-processorer (2,5 MB/Core-> 1,375 MB/Core).

Ovanstående topologi går även över till HC-seriens hypervisor-konfiguration. För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi pCores 0-1 och 24-25 (det vill säga de första två pCores på varje socket). Vi tilldelar sedan pNUMA-domäner alla återstående kärnor till den virtuella datorn. Den virtuella datorn kommer därför att se:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`kärnor per virtuell dator

Den virtuella datorn har ingen kunskap som pCores 0-1 och 24-25 inte fick till den. Därför exponerar den varje vNUMA som om den ursprungligen hade 22 kärnor.

Intel Xeon platina-, guld-och silver-CPU: er introducerar också ett interaktivt 2D-nätnätverk för kommunikation inom och utanför CPU-socketen. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens. Process fästning fungerar på virtuella datorer i HC-serien eftersom den underliggande kiseln exponeras för den virtuella gäst datorn. Mer information finns i [Intel Xeon SP-arkitektur](https://bit.ly/2RCYkiE).

Följande diagram visar ansvars fördelning av kärnor som är reserverade för Azure hypervisor och virtuell HC-serien.

![Uppdelning av kärnor som har reserver ATS för Azure hypervisor och HC-seriens virtuella datorer](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Specifikationer för maskin vara          | Virtuell dator i HC-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 44 (HT-inaktive rad)                 |
| Processor                              | Intel Xeon platina 8168 *        |
| CPU-frekvens (icke-AVX)          | 3,7 GHz (enkel kärna), 2.7-3.4 GHz (alla kärnor) |
| Minne                           | 8 GB/kärna (totalt 352)            |
| Lokal disk                       | 700 GB NVMe                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Nätverk                          | 50 GB Ethernet (40 GB kan användas) Azure andra gen SmartNIC * * * |

## <a name="software-specifications"></a>Programspecifikationer

| Programspecifikationer     | Virtuell dator i HC-serien          |
|-----------------------------|-----------------------|
| Maximal jobb storlek för MPI            | 13200 kärnor (300 virtuella datorer i en enda VMSS med singlePlacementGroup = true) |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage support       | STD + Premium (max 4 diskar) |
| OS-stöd för IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Stöd för Azure CycleCloud    | Yes                         |
| Azure Batch support         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om HPC VM-storlekar för [Linux](../../sizes-hpc.md) och [Windows](../../sizes-hpc.md) i Azure.

* Lär dig mer om [HPC](/azure/architecture/topics/high-performance-computing/) i Azure.
