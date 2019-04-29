---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c2908ef5c67665b5ba48879626370f977634dc83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776347"
---
GPU-optimerad VM storlekar är specialiserade virtuella datorer som är tillgängliga med en eller flera NVIDIA GPU: er. De här storlekarna är utformade för arbetsbelastningar för beräkningsintensiva, grafik och visualisering. Den här artikeln innehåller information om antalet och typen av GPU: er, virtuella processorer, diskar och nätverkskort. Storage dataflöde och nätverket bandbredd ingår också för varje storlek i den här grupperingen. 

* **NC, NCv2, NCv3, ND och NDv2** storlekarna är optimerade för beräkningsintensiva och nätverksintensiva program och algoritmer. Några exempel är CUDA - och OpenCL-baserade program och simuleringar, AI och Djupinlärning. NCv3-serien fokuserar på högpresterande arbetsbelastningar med NVIDIA: s Tesla V100 GPU.  ND-serien fokuserar på utbildning och inferensscenarier för djupinlärning. I serien används NVIDIA Tesla P40 GPU:er

* Den **NC-serien** har en Intel Xeon® E5-2690 v3 2,60 GHz-processor.

* Den **NCSv3**, **NCSv2**, och **ND** storlekar använder en Intel Xeon® E5-2690 v4 2,60 GHz-processorer.
                      
* **NV och NVv2** storlekar optimerade och utformade för fjärrvisualiserings, streaming, spel, kodning och VDI-scenarier med ramverk som OpenGL och DirectX.  Dessa virtuella datorer backas upp av NVIDIA Tesla M60 GPU.


## <a name="nc-series"></a>NC-serien

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

Virtuella datorer NC-serien drivs av den [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) kort. Användare kan bearbeta data snabbare genom att utnyttja CUDA för energiutforskningstillämpningar, krascha simuleringar, ray spårade rendering och djupinlärning. NC24r-konfiguration ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.


| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

## <a name="ncv2-series"></a>NCv2-serien

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

Virtuella datorer NCv2-serien drivs av [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU: er. Dessa GPU: erna kan tillhandahålla mer än 2 x dataprestanda NC-serien. Kunder kan dra nytta av de här uppdaterade GPU: erna för traditionella HPC-arbetsbelastningar, till exempel modellering av behållare, DNA sekvensering, proteinanalys, Monte Carlo-simuleringar och andra. Konfigurationen för NC24rs v2 ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna)-kvoten i prenumerationen inledningsvis 0 i varje region. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = ett P100-kort.

*RDMA-stöd

## <a name="ncv3-series"></a>NCv3-serien

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

Virtuella datorer i NCv3-serien drivs av [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU: er. Dessa GPU: erna kan tillhandahålla 1,5 gånger mer dataprestanda NCv2-serien. Kunder kan dra nytta av de här uppdaterade GPU: erna för traditionella HPC-arbetsbelastningar, till exempel modellering av behållare, DNA sekvensering, proteinanalys, Monte Carlo-simuleringar och andra. NC24rs v3 konfigurationen ger en låg fördröjning och stora dataflöden för tätt sammansatta parallella beräkningsarbetsbelastningar.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna)-kvoten i prenumerationen inledningsvis 0 i varje region. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = ett V100-kort.

*RDMA-stöd

## <a name="ndv2-series-preview"></a>NDv2-serien (förhandsversion)


Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

Infiniband: Stöds inte


NDv2-serien virtuella datorn är en nyhet i GPU-familjen som utformats för behoven hos de HPC, AI och maskininlärningsarbetsbelastningar. De drivs av sammankopplade 8 NVIDIA Tesla V100 NVLINK-GPU:er, 40 Intel Skylake-kärnor och 672 GiB systemminne. NDv2-instansen ger utmärkta FP32- och FP64-prestanda för HPC- och AI-arbetsbelastningar som använder Cuda, TensorFlow, Pytorch, Caffe och andra ramverk.

[Registrera dig och få åtkomst till dessa datorer under förhandsversionsperioden](https://aka.ms/ndv2signup).
<br>


| Storlek              | Virtuell processor | GPU              | Minne  | Nätverkskort (max.) | Max. Diskstorlek           | Max. datadiskar (1 023 GB som är var) | Maximal nätverksbandbredd | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672 giB | 8          | Tillfällig 1344 / 2948XIO | 32                             | 24 000 Mbit/s           |

## <a name="nd-series"></a>ND-serien

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

De virtuella datorerna ND-serien är en nyhet i GPU-familjen som utformats för AI och Djupinlärning arbetsbelastningar. De erbjuder utmärkta prestanda för utbildning och inferens. ND-instanserna drivs av [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU: er. Dessa instanser tillhandahåller utmärkta prestanda för enkel precision flytande punkt, för AI-arbetsbelastningar som använder Microsoft Cognitive Toolkit, TensorFlow, Caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnesstorlek (24 GB), vilket gör det möjligt att passa större neurala nätverksmodeller. Precis som NC-serien erbjuder ND-serien en konfiguration med ett sekundära nätverk med låg latens, högt dataflöde genom RDMA och InfiniBand-anslutning så att du kan köra storskaliga upplärningsjobb över flera GPU: er.

> [!IMPORTANT]
> För den här storleksfamilj är vCPU (kärna) kvot per region för din prenumeration inledningsvis till 0. [Begära en kvot för vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga regionen](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = ett P40-kort.

*RDMA-stöd

## <a name="nv-series"></a>NV-serien

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

De virtuella datorerna NV-serien drivs av [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA GRID teknik för desktop skrivbordsaccelererade program och virtuella skrivbord där kunder kan visualisera sina data eller simuleringar. Användarna kan visualisera grafikintensiva arbetsflöden på NV-instanserna och få överlägsen grafikkapacitet samt köra enskilda precisionsarbetsbelastningar som kodning och rendering. 

Varje GPU i NV-instanserna levereras med en GRID-licens. Denna licens ger dig flexibiliteten att använda en NV-instans som en virtuell dator för en enskild användare eller 25 samtidiga användare kan ansluta till den virtuella datorn för ett scenario med virtuella program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.

## <a name="nvv2-series-preview"></a>NVv2-serien (förhandsversion)

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

De virtuella datorerna NVv2-serien drivs av [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA GRID teknik med Intel Broadwell-processorer. Dessa virtuella datorer är avsedda för GPU accelerated grafikprogram och virtuella skrivbord där kunder vill visualisera sina data, simulera resultat att visa, arbeta med CAD- eller rendering och stream-innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision som kodning och rendering. NVv2 virtuella datorer stöd för Premium Storage och levereras med två gånger systemminne (RAM) jämfört med föregångaren NV-serien.  

Varje GPU i NVv2 instanser levereras med en GRID-licens. Denna licens ger dig flexibiliteten att använda en NV-instans som en virtuell dator för en enskild användare eller 25 samtidiga användare kan ansluta till den virtuella datorn för ett scenario med virtuella program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = ett halvt M60-kort.

 
