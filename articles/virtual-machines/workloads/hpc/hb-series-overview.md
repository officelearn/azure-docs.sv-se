---
title: Översikt över virtuella datorer i HB-serien – virtuella Azure-datorer | Microsoft-dokument
description: Läs mer om förhandsgranskningsstödet för VM-storlek i HB-serien i Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707765"
---
# <a name="hb-series-virtual-machines-overview"></a>Översikt över virtuella datorer i HB-serien

För att maximera HPC-programprestanda (High Performance Compute) på AMD EPYC krävs en genomtänkt metod för minneslokal och processplacering. Nedan beskriver vi AMD EPYC-arkitekturen och vår implementering av den på Azure för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän.

Fysiskt är en HB-serie 2 * 32-kärniga EPYC 7551-processorer för totalt 64 fysiska kärnor. Dessa 64 kärnor är indelade i 16 pNUMA-domäner (8 per socket), som var och en är fyra kärnor och kallas "CPU Complex" (eller "CCX"). Varje CCX har sin egen L3-cache, vilket är hur ett OS kommer att se en pNUMA/vNUMA-gräns. Ett par intilliggande CCXs delar åtkomst till två kanaler för fysisk DRAM (32 GB DRAM i HB-seriens servrar).

För att ge utrymme för Azure hypervisor att fungera utan att störa den virtuella datorn, reserverar vi fysisk pNUMA domän 0 (den första CCX). Vi tilldelar sedan pNUMA-domäner 1-15 (de återstående CCX-enheterna) för den virtuella datorn. Den virtuella datorn kommer att se:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`kärnor per virtuell dator

Den virtuella datorn, i sig, vet inte att pNUMA 0 inte gavs till den. Den virtuella datorn förstår pNUMA 1-15 som vNUMA 0-14, med 7 vNUMA på vSocket 0 och 8 vNUMA på vSocket 1. Även om detta är asymmetriskt, bör ditt operativsystem starta och fungera normalt. Senare i den här guiden instruerar vi hur du bäst kör MPI-program på den här asymmetriska NUMA-layouten.

Process-pinning fungerar på HB-serien virtuella datorer eftersom vi exponerar den underliggande kisel som är till gästen VM. Vi rekommenderar starkt processnålning för optimal prestanda och konsekvens.

Läs mer om [AMD EPYC-arkitektur](https://bit.ly/2Epv3kC) och [arkitekturer med flera chip](https://bit.ly/2GpQIMb) på LinkedIn. Mer detaljerad information finns i [HPC Tuning Guide för AMD EPYC-processorer](https://bit.ly/2T3AWZ9).

Följande diagram visar segregeringen av kärnor som är reserverade för Azure Hypervisor och den virtuella datorn i HB-serien.

![Segregering av kärnor som reserverats för Azure Hypervisor och HB-serien VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Specifikationer för HW                | HB-serien VM                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 60 (SMT inaktiverat)                |
| Processor                              | AMD EPYC 7551*                   |
| CPU-frekvens (icke-AVX)          | ~2,55 GHz (enkel + alla kärnor)   |
| Minne                           | 4 GB/kärna (240 totalt)            |
| Lokal disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Nätverk                          | 50 Gb Ethernet (40 Gb användbart) Azure andra Gen SmartNIC*** |

## <a name="software-specifications"></a>Specifikationer för programvara

| SW Specifikationer           |HB-serien VM           |
|-----------------------------|-----------------------|
| Max MPI-jobbstorlek            | 6000 kärnor (100 skalsatser för virtuella datorer) 1 2000 kärnor (200 skalsatser för virtuella datorer)  |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, Plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage-stöd       | Std + Premium (max 4 diskar) |
| OS-stöd för SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud-stöd    | Ja                         |
| Azure Batch-stöd         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Läs mer om HPC VM-storlekar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) i Azure.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
