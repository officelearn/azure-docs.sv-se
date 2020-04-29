---
title: Data behandling med höga prestanda på virtuella datorer i H-serien – Azure Virtual Machines
description: Lär dig mer om funktionerna i H-seriens virtuella datorer som är optimerade för HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76271025"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Data behandling med höga prestanda på virtuella datorer i H-serien

Data behandling med höga prestanda (HPC) på virtuella datorer i HB-serien och HC-serien möjliggör de mest optimerade HPC-prestanda för alla virtuella datorer i Azure. HPC-optimerade virtuella datorer används för att lösa några av de mest svåra matematiska problemen som: Fluid Dynamics, olje-och gas simulering och väder modellering.

Den här artikeln beskriver några viktiga funktioner i virtuella datorer i HB-serien och HC-serien, varför de virtuella datorerna fungerar bra i HPC-scenarier och hur du kommer igång.

## <a name="features-and-capabilities"></a>Funktioner

Virtuella datorer i HB-serien och HC-serien har utformats för att ge bästa möjliga HPC-prestanda, skalbarhet för Message Passing Interface (MPI) och kostnads effektivitet för HPC-arbetsbelastningar.

### <a name="message-passing-interface"></a>Gränssnitt för meddelande överföring

HB-serien och HC-serien stöder nästan alla MPI-typer och-versioner. Några av de vanligaste typerna av MPI-typer som stöds är: OpenMPI, MVAPICH2, Platform MPI, Intel MPI och alla RDMA-verb (Remote Direct Memory Access). Mer information finns i [Konfigurera Message Passing Interface för HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA och InfiniBand

RDMA-gränssnittet är standard på virtuella datorer i HB-serien och HC-serien. Instanser av RDMA-kapacitet kommunicerar över ett InfiniBand-nätverk, som arbetar med förbättrade data hastigheter (EDR) för virtuella datorer i HB-serien och HC-serien. RDMA-kompatibla instanser kan öka skalbarheten och prestandan för vissa MPI-program.

InfiniBand-konfigurationen som stöder virtuella datorer i HB-serien och HC-serien är icke-blockerande fett träd med en design med låg diameter för konsekvent RDMA-prestanda.

Se [Aktivera InfiniBand](enable-infiniband.md) för att lära dig mer om hur du konfigurerar InfiniBand på dina virtuella datorer i HB-serien eller HC-serien.

## <a name="get-started"></a>Kom igång

Bestäm först vilken virtuell dator i H-serien som du ska använda. Mer information om HPC-optimerade virtuella datorer finns i Översikt [över HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md). För specifikationer, se [storlekar för beräkning av virtuella datorer med höga prestanda](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

När du har valt och skapat en virtuell dator för ditt program måste du konfigurera den genom att aktivera InfiniBand. Information om hur du aktiverar InfiniBand i både virtuella Windows-och Linux-datorer finns i [Aktivera InfiniBand](enable-infiniband.md).

En viktig komponent i HPC-arbetsbelastningar är MPI. HB-serien och HC-serien stöder nästan alla MPI-typer och-versioner. Mer information finns i [Konfigurera Message Passing Interface för HPC](setup-mpi.md).

När du har valt din VM-serie, konfigurerat InfiniBand och MPI är du redo att börja skapa dina HPC-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

- Läs översikten över [HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md) för att lära dig mer om viktiga skillnader och specifikationer.

- För en högre nivå, arkitektonisk visning av aktiva HPC-arbetsbelastningar, se [HPC (data behandling med höga prestanda) i Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
