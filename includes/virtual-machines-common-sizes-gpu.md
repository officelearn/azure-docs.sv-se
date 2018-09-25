---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/10/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: e9f88a018ba0f40d41bf7ad17a74850a9edf8ae6
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043869"
---
GPU-optimerad VM storlekar är specialiserade virtuella datorer som är tillgängliga med en eller flera NVIDIA GPU: er. De här storlekarna är utformade för arbetsbelastningar för beräkningsintensiva, grafik och visualisering. Den här artikeln innehåller information om antalet och typen av GPU: er, virtuella processorer, diskar och nätverkskort. Storage dataflöde och nätverket bandbredd ingår också för varje storlek i den här grupperingen. 

* **NC, NCv2, NCv3 och ND** storlekarna är optimerade för beräkningsintensiva och nätverksintensiva program och algoritmer. Några exempel är CUDA - och OpenCL-baserade program och simuleringar, AI och Djupinlärning. NCv3-serien fokuserar på högpresterande arbetsbelastningar med NVIDIA: s Tesla V100 GPU.  ND-serien fokuserar på utbildning och inferensscenarier för djupinlärning. I serien används NVIDIA Tesla P40 GPU:er
* **NV och NVv2** storlekar optimerade och utformade för fjärrvisualiserings, streaming, spel, kodning och VDI-scenarier med ramverk som OpenGL och DirectX.  Dessa virtuella datorer backas upp av NVIDIA Tesla M60 GPU.


## <a name="nc-series"></a>NC-serien

Premium-lagring: Stöds inte

Premium Storage cachelagring: Stöds inte

Virtuella datorer NC-serien drivs av den [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) kort. Användare kan bearbeta data snabbare genom att utnyttja CUDA för energiutforskningstillämpningar, krascha simuleringar, ray spårade återgivning, djupinlärning och mycket mer. NC24r-konfiguration ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.


| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

## <a name="ncv2-series"></a>NCv2-serien

Premium Storage: stöds

Cachelagring för Premium Storage: stöds

Virtuella datorer NCv2-serien drivs av [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU: er. Dessa GPU: erna kan tillhandahålla mer än 2 x dataprestanda NC-serien. Kunder kan dra nytta av de här uppdaterade GPU: erna för traditionella HPC-arbetsbelastningar, till exempel modellering av behållare, DNA sekvensering, proteinanalys, Monte Carlo-simuleringar och andra. Konfigurationen för NC24rs v2 ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna)-kvoten i prenumerationen inledningsvis 0 i varje region. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = ett P100-kort.

*RDMA-stöd

## <a name="ncv3-series"></a>NCv3-serien

Premium Storage: stöds

Cachelagring för Premium Storage: stöds

Virtuella datorer i NCv3-serien drivs av [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU: er. Dessa GPU: erna kan tillhandahålla 1,5 gånger mer dataprestanda NCv2-serien. Kunder kan dra nytta av de här uppdaterade GPU: erna för traditionella HPC-arbetsbelastningar, till exempel modellering av behållare, DNA sekvensering, proteinanalys, Monte Carlo-simuleringar och andra. NC24rs v3 konfigurationen ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna)-kvoten i prenumerationen inledningsvis 0 i varje region. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = ett V100-kort.

*RDMA-stöd

## <a name="nd-series"></a>ND-serien

Premium Storage: stöds

Cachelagring för Premium Storage: stöds

De virtuella datorerna ND-serien är en nyhet i GPU-familjen som är utformat för AI och Djupinlärning arbetsbelastningar. De erbjuder utmärkta prestanda för utbildning och inferens. ND-instanserna drivs av [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU: er. Dessa instanser tillhandahåller utmärkta prestanda för enkel precision flytande punkt, för AI-arbetsbelastningar som använder Microsoft Cognitive Toolkit, TensorFlow, Caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnesstorlek (24 GB), vilket gör det möjligt att passa större neurala nätverksmodeller. Precis som NC-serien erbjuder ND-serien en konfiguration med ett sekundära nätverk med låg latens, högt dataflöde genom RDMA och InfiniBand-anslutning så att du kan köra storskaliga upplärningsjobb över flera GPU: er.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna) kvot per region för din prenumeration inledningsvis till 0. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = ett P40-kort.

*RDMA-stöd

## <a name="nv-series"></a>NV-serien

Premium-lagring: Stöds inte

Premium Storage cachelagring: Stöds inte

De virtuella datorerna NV-serien drivs av [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA GRID teknik för desktop skrivbordsaccelererade program och virtuella skrivbord där kunder kan visualisera sina data eller simuleringar. Användarna kan visualisera grafikintensiva arbetsflöden på NV-instanserna och få överlägsen grafikkapacitet samt köra enskilda precisionsarbetsbelastningar som kodning och rendering. 

Varje GPU i NV-instanserna levereras med en GRID-licens. Denna licens ger dig flexibiliteten att använda en NV-instans som en virtuell dator för en enskild användare eller 25 samtidiga användare kan ansluta till den virtuella datorn för ett scenario med virtuella program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.

## <a name="nvv2-series-preview"></a>NVv2-serien (förhandsversion)

Premium Storage: stöds

Cachelagring för Premium Storage: stöds

De virtuella datorerna NVv2-serien drivs av [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA GRID teknik med Intel Broadwell-processorer. Dessa virtuella datorer är avsedda för GPU accelerated grafikprogram och virtuella skrivbord där kunder vill visualisera sina data, simulera resultat att visa, arbeta med CAD- eller rendering och stream-innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision som kodning och rendering. NVv2 virtuella datorer stöd för Premium Storage och levereras med två gånger systemminne (RAM) jämfört med föregångaren NV-serien.  

Varje GPU i NVv2 instanser levereras med en GRID-licens. Denna licens ger dig flexibiliteten att använda en NV-instans som en virtuell dator för en enskild användare eller 25 samtidiga användare kan ansluta till den virtuella datorn för ett scenario med virtuella program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 8 | 4 | 100 |

1 GPU = ett halvt M60-kort.

 
