---
title: NDv2-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i NDv2-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1aa2a6402a58ba69a7b5999803bb10d48169a035
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267441"
---
# <a name="updated-ndv2-series-preview"></a>Uppdaterad NDv2-serien (för hands version)

Den virtuella datorn i NDv2-serien är ett nytt tillägg till GPU-familjen som är utformad för att behöva de mest krävande GPU: n för GPU-accelererad GPU, maskin inlärning, simulering och HPC-arbetsbelastningar.

NDv2 drivs av 8 NVIDIA Tesla V100 NVLINK-anslutna GPU: er, var och en med 32 GB GPU-minne. Varje NDv2 VM har också 40-kärnor som inte är trådade Intel Xeon Platin 8168 (Skylake) och 672 GiB system minne.

NDv2-instanser ger utmärkta prestanda för HPC-och AI-arbetsbelastningar som använder CUDA GPU-optimerade beräknings kärnor och de många AI-, ML-och Analytics-verktyg som har stöd för GPU-acceleration "out-of-box", till exempel TensorFlow, Pytorch, caffe, RAPIDS och andra ramverk.

I stor skala är NDv2 byggd för både beräknings intensiva data (med 8 GPU-processorer per virtuell dator) och skalbarhet (vilket innebär att flera virtuella datorer fungerar tillsammans) arbets belastningar. NDv2-serien stöder nu 100-Gigabit InfiniBand EDR-nätverk, som liknar det som finns på HB-serien med HPC-VM, för att tillåta kluster med hög prestanda för parallella scenarier, inklusive distribuerad utbildning för AI och ML. Detta Server dels nätverk har stöd för alla större InfiniBand-protokoll, inklusive de som används av NVIDIA: s NCCL2-bibliotek, vilket möjliggör sömlös klustring av GPU: er.


> [!NOTE]
> När du [aktiverar InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) på den virtuella datorn ND40rs_v2 ska du använda Mellanox ofed-drivrutinen (4,7-1.0.0.1).
>
> På grund av ett ökat GPU-minne kräver den nya ND40rs_v2 virtuella datorn användningen av [virtuella datorer i generation 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) och Marketplace-avbildningar. 
>
> [Registrera dig för att få tidig till gång till den virtuella NDv2-datorn för hands version.](https://aka.ms/AzureNDrv2Preview)
>
> OBS! den ND40s_v2 med 16 GB minne per GPU är inte längre tillgänglig för för hands versionen och har ersatts av den uppdaterade ND40rs_v2.

<br>

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

InfiniBand: stöds

| Storlek | Virtuell processor | Minne: GiB | Temp-lagring (SSD): GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximal nätverks bandbredd | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbit/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera NVIDIA GPU-drivrutiner.

[NVidia GPU-drivrutinen](./extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](./extensions/hpccompute-gpu-windows.md) . Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](./extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [N-seriens installations program för GPU-drivrutiner för Windows](./windows/n-series-driver-setup.md) eller [N-serien GPU-drivrutin installation för Linux](./linux/n-series-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
