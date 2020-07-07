---
title: Översikt över virtuella datorer i HB-serien – Azure Virtual Machines | Microsoft Docs
description: Läs mer om för hands versionen av storleken på virtuella datorer i HB-serien i Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707765"
---
# <a name="hb-series-virtual-machines-overview"></a>Översikt över virtuella datorer i HB-serien

Att maximera prestanda för hög prestanda beräkning (HPC) i AMD EPYC kräver en metod för att lokalisera minnes utrymme och processer på ett genomtänkt sätt. Nedan ska vi disponera AMD EPYC-arkitekturen och vår implementering av IT på Azure för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän.

Fysiskt är en HB-serie 2 * 32 kärnor EPYC 7551 CPU: er för totalt 64 fysiska kärnor. Dessa 64 kärnor är indelade i 16 pNUMA-domäner (8 per socket) som är fyra kärnor och kallas "processor komplex" (eller "CCX"). Varje CCX har sin egen L3-cache, vilket är hur ett operativ system kommer att se en pNUMA/vNUMA-gränsen. Ett par intilliggande CCXs delar åtkomst till två kanaler av fysiskt DRAM (32 GB RAM-minnen i HB-serie servrar).

För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi fysisk pNUMA-domän 0 (den första CCX-datorn). Sedan tilldelar vi pNUMA-domäner 1-15 (återstående CCX-enheter) för den virtuella datorn. Den virtuella datorn visas:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`kärnor per virtuell dator

Den virtuella datorn vet inte att pNUMA 0 har tilldelats den. Den virtuella datorn förstår pNUMA 1-15 som vNUMA 0-14, med 7 vNUMA på vSocket 0 och 8 vNUMA på vSocket 1. Även om detta är asymmetriskt bör operativ systemet starta och fungerar normalt. Senare i den här hand boken beskriver vi hur du bäst kör MPI-program på den här asymmetriska NUMA-layouten.

Process fästning fungerar på virtuella datorer i HB-serien eftersom vi exponerar den underliggande kiseln som-är till den virtuella gäst datorn. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens.

Se mer på [AMD EPYC-arkitektur](https://bit.ly/2Epv3kC) och [multi-chip-arkitekturer](https://bit.ly/2GpQIMb) på LinkedIn. Mer detaljerad information finns i HPC- [justerings guiden för AMD EPYC-processorer](https://bit.ly/2T3AWZ9).

Följande diagram visar ansvars fördelning av kärnor som är reserverade för Azure hypervisor och den virtuella HB-serien.

![Uppdelning av kärnor som är reserverade för Azure hypervisor och HB-serien VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Specifikationer för maskin vara                | Virtuell dator i HB-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 60 (SMT inaktive rad)                |
| Processor                              | AMD EPYC 7551 *                   |
| CPU-frekvens (icke-AVX)          | ~ 2,55 GHz (enkel + alla kärnor)   |
| Minne                           | 4 GB/kärna (totalt 240)            |
| Lokal disk                       | 700 GB NVMe                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Nätverk                          | 50 GB Ethernet (40 GB kan användas) Azure andra gen SmartNIC * * * |

## <a name="software-specifications"></a>Programspecifikationer

| SW-specifikationer           |Virtuell dator i HB-serien           |
|-----------------------------|-----------------------|
| Maximal jobb storlek för MPI            | 6000 kärnor (100 Virtual Machine Scale set) 12000 kärnor (200 Virtual Machine Scale set)  |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage support       | STD + Premium (max 4 diskar) |
| OS-stöd för IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Stöd för Azure CycleCloud    | Ja                         |
| Azure Batch support         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om HPC VM-storlekar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) i Azure.

* Lär dig mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
