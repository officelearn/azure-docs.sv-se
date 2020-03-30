---
title: Översikt över virtuella datorer i HC-serien – virtuella Azure-datorer| Microsoft-dokument
description: Läs mer om förhandsgranskningsstödet för VM-storlek i HC-serien i Azure.
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
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906742"
---
# <a name="hc-series-virtual-machine-overview"></a>Översikt över virtuell dator i HC-serien

För att maximera HPC-programprestanda på Intel Xeon Scalable-processorer krävs en genomtänkt metod för processplacering på den här nya arkitekturen. Här beskriver vi vår implementering av den på virtuella azure HC-serien för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän. På samma sätt kommer vi att använda termen "pCore" för att hänvisa till fysiska CPU-kärnor, och "vCore" för att hänvisa till virtualiserade CPU-kärnor.

Fysiskt är en HC-server 2 * 24-kärniga Intel Xeon Platinum 8168-processorer för totalt 48 fysiska kärnor. Varje CPU är en enda pNUMA-domän och har enhetlig åtkomst till sex kanaler för DRAM. Intel Xeon Platinum-processorer har en 4x större L2-cache än tidigare generationer (256 KB/core -> 1 MB/core), samtidigt som L3-cachen minskas jämfört med tidigare Intel-processorer (2,5 MB/core -> 1,375 MB/kärna).

Ovanstående topologi bär över till HC-serien hypervisor konfiguration också. För att ge utrymme för Azure hypervisor att fungera utan att störa den virtuella datorn, reserverar vi pCores 0-1 och 24-25 (det vill vara de första 2 pCore på varje socket). Vi tilldelar sedan pNUMA-domäner alla återstående kärnor till den virtuella datorn. Den virtuella datorn kommer alltså att se:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`kärnor per virtuell dator

Den virtuella datorn har ingen kunskap om att pCores 0-1 och 24-25 inte gavs till den. Således exponerar det varje vNUMA som om det inbyggt hade 22 kärnor.

Intel Xeon Platinum, Gold och Silver processorer introducerar också en on-die 2D mesh-nätverk för kommunikation inom och utanför CPU-uttaget. Vi rekommenderar starkt processnålning för optimal prestanda och konsekvens. Processbenad fungerar på virtuella datorer i HC-serien eftersom det underliggande kiselt är exponerat som det är för gästdatorn. Mer information finns i [Intel Xeon SP-arkitekturen](https://bit.ly/2RCYkiE).

Följande diagram visar segregeringen av kärnor som är reserverade för Azure Hypervisor och VM-serien i HC-serien.

![Segregering av kärnor som reserverats för Azure Hypervisor och HC-serien VM](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Maskinvaruspecifikationer          | HC-serien VM                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 44 (HT inaktiverad)                 |
| Processor                              | Intel Xeon Platina 8168*        |
| CPU-frekvens (icke-AVX)          | 3,7 GHz (en kärna), 2,7-3,4 GHz (alla kärnor) |
| Minne                           | 8 GB/kärna (352 totalt)            |
| Lokal disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Nätverk                          | 50 Gb Ethernet (40 Gb användbart) Azure andra Gen SmartNIC*** |

## <a name="software-specifications"></a>Specifikationer för programvara

| Specifikationer för programvara     | HC-serien VM          |
|-----------------------------|-----------------------|
| Max MPI-jobbstorlek            | 13200 kärnor (300 virtuella datorer i en enda VMSS med singlePlacementGroup=true) |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, Plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage-stöd       | Std + Premium (max 4 diskar) |
| OS-stöd för SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud-stöd    | Ja                         |
| Azure Batch-stöd         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Läs mer om HPC VM-storlekar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) i Azure.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
