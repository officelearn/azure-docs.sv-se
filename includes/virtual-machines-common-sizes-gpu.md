---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 0325fc8cabc43988fb27a307921977b9b487c123
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286173"
---
GPU-optimerade VM-storlekar är specialiserade virtuella datorer som är tillgängliga med enkla eller flera NVIDIA GPU: er. De här storlekarna är utformade för beräknings intensiva, grafik intensiva och visualiserings arbets belastningar. Den här artikeln innehåller information om antalet och typen av GPU, virtuella processorer, data diskar och nätverkskort. Lagrings data flöde och nätverks bandbredd ingår också för varje storlek i grupperingen.

* **NC, NCv2, NCv3** storlek är optimerade för beräknings intensiva och nätverks intensiva program och algoritmer. Några exempel är CUDA-och OpenCL-baserade program och simuleringar, AI och djup inlärning. NCv3-serien fokuserar på högpresterande data behandlings arbets belastningar med NVIDIA: s Tesla V100-GPU. NC-serien använder Intel Xeon E5-2690 v3 2.60 GHz v3-processorn, och de virtuella datorerna i NCv2-serien och NCv3-serien använder Intel Xeon E5-2690 v4-processorn (Broadwell).

* **Nd och NDv2** ND-serien fokuserar på inlärnings-och härlednings scenarier för djup inlärning. Den använder NVIDIA Tesla P40-GPU och Intel Xeon E5-2690 v4-processorn (Broadwell). NDv2-serien använder Intel Xeon platina 8168-processorn (Skylake).

* **NV-och NVv3** -storlekarna är optimerade och utformade för fjärrvisualiserings-, strömnings-, spel-, kodnings-och VDI-scenarier med ramverk som OpenGL och DirectX.  De här virtuella datorerna backas upp av NVIDIA Tesla M60-GPU: n.

## <a name="nc-series"></a>NC-serien

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

Virtuella datorer i NC-serien drivs av [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) -kortet och Intel Xeon E5-2690 v3-processorn (Haswell). Användare kan gå igenom data snabbare genom att använda CUDA för energi gransknings program, krasch simulering, Ray-spårad åter givning, djup inlärning med mera. NC24r-konfigurationen ger ett nätverks gränssnitt med låg latens och hög genom strömning som är optimerat för tätt sammansatta parallella dator arbets belastningar.

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

## <a name="ncv2-series"></a>NCv2-serien

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

Virtuella datorer i NCv2-serien drivs av [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) -GPU: er. Dessa GPU: er kan ge mer än dubbelt så många beräknings prestanda som NC-serien. Kunderna kan dra nytta av dessa uppdaterade GPU: er för traditionella HPC-arbetsbelastningar som till exempel behållar modellering, DNA-sekvensering, protein analys, Monte Carlo-simuleringar och andra. Förutom GPU: er är virtuella datorer i NCv2-serien också baserade på Broadwell-processorer (Intel Xeon E5-2690 v4).

NC24rs v2-konfigurationen ger ett nätverks gränssnitt med låg fördröjning och hög genom strömning som är optimerat för tätt sammansatta parallella dator arbets belastningar.

> [!IMPORTANT]
> För den här storleks familjen anges vCPU-kvoten (kärnor) i din prenumeration till 0 i varje region. [Begär en vCPU kvot ökning](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här familjen i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = ett P100-kort.

*RDMA-stöd

## <a name="ncv3-series"></a>NCv3-serien

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

Virtuella datorer i NCv3-serien drivs av [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) -GPU: er. Dessa GPU: er kan ge 1,5 x beräknings prestanda i NCv2-serien. Kunderna kan dra nytta av dessa uppdaterade GPU: er för traditionella HPC-arbetsbelastningar som till exempel behållar modellering, DNA-sekvensering, protein analys, Monte Carlo-simuleringar och andra. NC24rs v3-konfigurationen ger ett nätverks gränssnitt med låg fördröjning och hög genom strömning som är optimerat för tätt sammansatta parallella data behandlings arbets belastningar. Förutom GPU: er är virtuella datorer i NCv3-serien också baserade på Broadwell-processorer (Intel Xeon E5-2690 v4).

> [!IMPORTANT]
> För den här storleks familjen anges vCPU-kvoten (kärnor) i din prenumeration till 0 i varje region. [Begär en vCPU kvot ökning](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här familjen i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = ett V100-kort.

*RDMA-stöd

## <a name="ndv2-series-preview"></a>NDv2-serien (för hands version)

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

InfiniBand Stöds inte

NDv2-seriens virtuella dator är ett nytt tillägg till GPU-familjen som är utformad för behoven hos arbets belastningarna HPC, AI och Machine Learning. Den drivs av 8 NVIDIA Tesla V100 NVLINK-anslutna GPU: er och 40 Intel Xeon Platin 8168 (Skylake) kärnor och 672 GiB av system minne. NDv2-instansen ger utmärkta FP32- och FP64-prestanda för HPC- och AI-arbetsbelastningar som använder Cuda, TensorFlow, Pytorch, Caffe och andra ramverk.

[Registrera dig och få till gång till de här datorerna under för hands versionen](https://aka.ms/ndv2signup).
<br>

| Size | Virtuell processor | GPU | Minne | Nätverkskort (max.) | GiB SSD (temp Storage) | Max. data diskar | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Maximal nätverks bandbredd | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000/800 | 24000 Mbit/s |

## <a name="nd-series"></a>ND-serien

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

De virtuella datorerna i ND-serien är ett nytt tillägg till GPU-familjen som är utformad för AI-och djup inlärnings arbets belastningar. De erbjuder utmärkt prestanda för utbildning och härledning. ND-instanser drivs av [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) -GPU: er och Intel Xeon E5-2690 v4-processorer (Broadwell). Dessa instanser ger utmärkta prestanda för flytt ALS åtgärder med enkel precision, för AI-arbetsbelastningar som använder Microsoft Cognitive Toolkit, TensorFlow, caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnesstorlek (24 GB), vilket gör det möjligt att passa större neurala nätverksmodeller. I likhet med NC-serien erbjuder ND-serien en konfiguration med ett sekundärt nätverk med låg latens, högt data flöde via RDMA och InfiniBand-anslutning så att du kan köra storskaliga utbildnings jobb över flera GPU: er.

> [!IMPORTANT]
> För den här storleks familjen ställs vCPU (Core)-kvoten per region i din prenumeration till 0. [Begär en vCPU kvot ökning](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här familjen i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = ett P40-kort.

*RDMA-stöd

## <a name="nv-series"></a>NV-serien

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

De virtuella datorerna med NV-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA grid-teknik för snabbare program och virtuella skriv bord där kunderna kan visualisera data eller simuleringar. Användare kan visualisera sina grafik intensiva arbets flöden på NV-instanserna för att få överlägsen grafik kapacitet och dessutom köra enskilda precisions arbets belastningar som kodning och åter givning. Virtuella datorer i NV-serien drivs också av Intel Xeon E5-2690 v3-processorer (Haswell).

Varje GPU i NV-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.

## <a name="nvv3-series--sup1sup"></a>NVv3-serie <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

De virtuella datorerna i NVv3-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-och NVIDIA grid-teknik med Intel E5-2690 v4-processorer (Broadwell). Dessa virtuella datorer är avsedda för GPU-accelererade grafik program och virtuella skriv bord där kunder vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision som kodning och rendering. NVv3 Virtual Machines stöder Premium Storage och levereras med två gånger system minnet (RAM) jämfört med dess föregående NV-serie.  

Varje GPU i NVv3-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = ett halvt M60-kort.

<sup>1</sup> NVv3 VM-funktioner Intel Hyper-Threading-teknik
