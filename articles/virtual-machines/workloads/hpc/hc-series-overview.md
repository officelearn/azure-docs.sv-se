---
title: Översikt över HC-serien VM – Azure Virtual Machines | Microsoft Docs
description: Läs mer om förhandsversionsstöd för HC-serien VM-storlek i Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: f96a1179c103dd9dfb4d358572f9a9adbe24b977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809879"
---
# <a name="hc-series-virtual-machine-overview"></a>Översikt över virtuella datorer för HC-serien

Maximera HPC programprestanda på skalbara Intel Xeon-processorer kräver en genomtänkt metod för placering av processen på den nya arkitekturen. Vi beskriver här, vår implementering av det på Azure virtuella datorer HC-serien för HPC-program. Vi använder termen ”pNUMA” för att referera till en fysisk NUMA-domän och ”vNUMA” att referera till en virtuell NUMA-domän. På samma sätt kan vi använder termen ”pCore” för att referera till fysiska processorkärnor och ”vCore” att referera till virtualiserade CPU-kärnor.

Fysiskt, en HC-server är 2 * 24 kärnor Intel Xeon Platinum 8168 processorer totalt 48 fysiska kärnor. Varje processor är en enda pNUMA domän och har unified åtkomst till sex DRAM-kanaler. Intel Xeon Platina processorer funktion a 4 x större L2 cacheminnet än i tidigare generationerna (256 KB/core-> 1 MB/kärna), samtidigt minska L3-cache jämfört med tidigare Intel-processorer (2,5 MB/core-> 1.375 MB/core).

Ovanstående topologin överförs till HC-serien hypervisor-konfigurationen samt. För att ge utrymme för Azure hypervisor-programmet att fungera utan att störa den virtuella datorn, förbehåller vi oss pCores 0-1 och 24-25 (det vill säga de första 2 pCores för varje socket). Vi sedan tilldela pNUMA domäner alla återstående kärnor till den virtuella datorn. Därför kommer att se den virtuella datorn:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` kärnor per virtuell dator

Den virtuella datorn har ingen information att pCores 0-1 och 24-25 inte anges till den. Därför exponerar varje vNUMA som om den hade internt 22 kärnor.

Intel Xeon Platina, guld och Silver processorer medför också ett på chips 2D nät nätverk för kommunikation inom och utanför till CPU-socket. Vi rekommenderar starkt processen fästa för optimala prestanda och konsekvens. Processen fästa fungerar på virtuella datorer HC-serien eftersom den underliggande silicon exponeras som – är att den Virtuella gästdatorn. Mer information om Intel Xeon SP-arkitektur på: https://bit.ly/2RCYkiE

Följande diagram visar uppdelning av kärnor reserverade för Azure-Hypervisor och virtuell HC-serien.

![Uppdelning av kärnor som är reserverade för Azure-Hypervisor och virtuell HC-serien](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Maskinvaruspecifikationer          | Virtuell HC-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 40 (HT inaktiverad)                 |
| Processor                              | Intel Xeon Platinum 8168 *        |
| CPU-frekvens (icke-AVX)          | 3,7 GHz (en kärna) 2.7 3.4 GHz (alla kärnor) |
| Minne                           | 8 GB/kärnor (totalt antal 352)            |
| Lokal disk                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Nätverk                          | 50 Gb Ethernet (40 Gb användbara) Azure andra Gen SmartNIC *** |

## <a name="software-specifications"></a>Programvaruspecifikationer

| Programvaruspecifikationer     | Virtuell HC-serien          |
|-----------------------------|-----------------------|
| Max MPI Jobbstorlekar            | 4400 kärnor (100 VM-skalningsuppsättningar), 8800 kärnor (200 VM-skalningsuppsättningar) |
| MPI-stöd                 | MVAPICH2, OpenMPI, MPICH, plattform MPI, Intel MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage-Support       | Standard och Premium (högst 4 diskar) |
| OS-stöd för SR-IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Azure CycleCloud Support    | Ja                         |
| Azure Batch-stöd         | Ja                         |

## <a name="next-steps"></a>Nästa steg

* Läs mer om HPC VM-storlekar för [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) och [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) i Azure.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
