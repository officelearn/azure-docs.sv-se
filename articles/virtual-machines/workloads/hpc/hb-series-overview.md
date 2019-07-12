---
title: Översikt över HB-serien VM – Azure Virtual Machines | Microsoft Docs
description: Läs mer om förhandsversionsstöd för HB-serien VM-storlek i Azure.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707765"
---
# <a name="hb-series-virtual-machines-overview"></a>Översikt över virtuella datorer HB-serien

Maximera högpresterande compute (HPC) programprestanda på AMD EPYC kräver en genomtänkt metoden minne ort och processen placeringen. Nedan visas vi AMD EPYC-arkitekturen och vår implementering av det på Azure för HPC-program. Vi använder termen ”pNUMA” för att referera till en fysisk NUMA-domän och ”vNUMA” att referera till en virtuell NUMA-domän.

Fysiskt, en HB-serien är 2 * 32 kärnor EPYC 7551 CPU: er sammanlagt 64 fysiska kärnor. Dessa 64 kärnor är uppdelad i 16 pNUMA domäner (8 per socket), som är fyra kärnor och kallas en ”CPU komplexa” (eller ”CCX”). Varje CCX har sin egen L3-cacheminne, vilket är hur ett operativsystem ser en pNUMA/vNUMA gräns. Ett par med angränsande CCXs delar åtkomst till två kanaler av fysiska DRAM (32 GB DRAM i HB-serien servrar).

För att ge utrymme för Azure hypervisor-programmet att fungera utan att störa den virtuella datorn, förbehåller vi oss fysiska pNUMA domän 0 (första CCX). Vi kan sedan tilldela pNUMA domäner 1 – 15 (de återstående CCX-enheterna) för den virtuella datorn. Den virtuella datorn visas:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` kärnor per virtuell dator

Virtuellt datorn, inte att visa den pNUMA 0 inte angavs till den. Den virtuella datorn förstår pNUMA 1 – 15 som vNUMA 0-14, med 7 vNUMA på vSocket 0 och 8 vNUMA på vSocket 1. Även om det är asymmetrisk ska ditt operativsystem starta och fungera normalt. Senare i den här guiden ber vi hur du bäst för att köra MPI-program på den här asymmetrisk NUMA-layouten.

Processen fästa fungerar på virtuella datorer HB-serien eftersom vi exponerar underliggande silicon som – är att den Virtuella gästdatorn. Vi rekommenderar starkt processen fästa för optimala prestanda och konsekvens.

Läs mer på [AMD EPYC arkitektur](https://bit.ly/2Epv3kC) och [flera chip arkitekturer](https://bit.ly/2GpQIMb) på LinkedIn. Mer information finns i den [HPC Justeringsguide för AMD EPYC processorer](https://bit.ly/2T3AWZ9).

Följande diagram visar uppdelning av kärnor reserverade för Azure-Hypervisor och virtuell HB-serien.

![Uppdelning av kärnor som är reserverade för Azure-Hypervisor och virtuell HB-serien](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| HW-specifikationer                | Virtuell HB-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 60 (SMT inaktiverad)                |
| Processor                              | AMD EPYC 7551*                   |
| CPU-frekvens (icke-AVX)          | ~2.55 GHz (en + alla kärnor)   |
| Minne                           | 4 GB/kärnor (totalt 240)            |
| Lokal disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Nätverk                          | 50 Gb Ethernet (40 Gb användbara) Azure andra Gen SmartNIC *** |

## <a name="software-specifications"></a>Programvaruspecifikationer

| SV specifikationer           |Virtuell HB-serien           |
|-----------------------------|-----------------------|
| Max MPI Jobbstorlekar            | 6000 kärnor (100 VM-skalningsuppsättningar) 12000 kärnor (200 VM-skalningsuppsättningar)  |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage-Support       | Standard och Premium (högst 4 diskar) |
| OS-stöd för SR-IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Azure CycleCloud Support    | Ja                         |
| Azure Batch-stöd         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Läs mer om HPC VM-storlekar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) i Azure.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
