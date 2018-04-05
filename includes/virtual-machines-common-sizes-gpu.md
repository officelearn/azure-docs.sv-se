---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 03/30/2018
ms.author: danlep;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 5457ac8bd229889ed2b96354c44066959c00c64f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
GPU optimerade VM storlekar är särskilda virtuella datorer som är tillgängliga med en eller flera NVIDIA GPU. Dessa storlekar är utformade för beräkningsintensiva, grafik och visualisering arbetsbelastningar. Den här artikeln innehåller information om antalet och typen av GPU-kort, vCPUs, datadiskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

* **NC, NCv2, NCv3 och ND** storlekar som är optimerade för beräkningsintensiva och nätverks-intensiva program och algoritmer, inklusive CUDA - och OpenCL-baserade program och simulering, AI och djup Learning. 
* **NV** storlekar är optimerad och utformade för visualisering av fjärråtkomst, strömning, spel, kodning och VDI-scenarier som använder ramar, till exempel OpenGL och DirectX.  


## <a name="nc-series"></a>NC-serien

NC-serien VMs drivs av den [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) kort. Användare kan matar via data snabbare genom att utnyttja CUDA för energi utforskning program kraschar simulering, ray spårade återgivning, djup learning och mycket mer. NC24r konfigurationen ger en låg latens, hög genomströmning nätverksgränssnittet som optimerats för tätt kopplade parallell databearbetning arbetsbelastningar.


| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 340 | 1 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

## <a name="ncv2-series"></a>NCv2-serien

NCv2-serien VMs drivs av [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU-kort. Dessa GPU-kort kan ge mer än 2 x beräkningar prestanda för NC-serien. Kunder kan dra nytta av dessa uppdaterade GPU för traditionella HPC-arbetsbelastningar som till exempel behållare modellering, DNA ordningsföljd, protein analys, Monte Carlo-simulering och andra. NC24rs v2 konfigurationen tillhandahåller en låg latens, hög genomströmning nätverksgränssnittet som optimerats för tätt kopplade parallell databearbetning arbetsbelastningar.

> [!IMPORTANT]
> För den här storleken familj är kvoten vCPU (core) i din prenumeration inledningsvis till 0 i varje region. [Begära en vCPU databaskvot](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga region](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | ---  |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = en P100-kort.

*RDMA-stöd

## <a name="ncv3-series"></a>NCv3-serien

NCv3-serien VMs drivs av [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU-kort. Dessa GPU-kort kan ge 1,5 gånger beräkningar prestandan hos NCv2-serien. Kunder kan dra nytta av dessa uppdaterade GPU för traditionella HPC-arbetsbelastningar som till exempel behållare modellering, DNA ordningsföljd, protein analys, Monte Carlo-simulering och andra. NC24rs v3 konfigurationen tillhandahåller en låg latens, hög genomströmning nätverksgränssnittet som optimerats för tätt kopplade parallell databearbetning arbetsbelastningar.

> [!IMPORTANT]
> För den här storleken familj är kvoten vCPU (core) i din prenumeration inledningsvis till 0 i varje region. [Begära en vCPU databaskvot](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga region](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = en V100-kort.

*RDMA-stöd

## <a name="nd-series"></a>ND-serien

ND-serien virtuella datorer är en nyhet i GPU-familjen för AI och djup Learning arbetsbelastningar. De ger utmärkt prestanda för utbildning och härledning. ND instanser drivs av [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU-kort. Dessa instanser ger utmärkt prestanda för enkel precision flytande punkt, för AI-arbetsbelastningar som använder Microsoft kognitiva Toolkit, TensorFlow, Caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnesstorlek (24 GB), vilket gör det möjligt att passa större neurala nätverksmodeller. Som NC-serien, ND-serien ger en konfiguration med en sekundär låg latens, hög genomströmning nätverk via RDMA, och InfiniBand-anslutningar så att du kan köra storskaliga utbildning jobb utsträckning många GPU-kort.

> [!IMPORTANT]
> För den här storleken-serien är vCPU (core) kvot per område i din prenumeration inledningsvis till 0. [Begära en vCPU databaskvot](../articles/azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgängliga region](https://azure.microsoft.com/regions/services/).
>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 32 | 8 |

1 GPU = en P40-kort.

*RDMA-stöd

## <a name="nv-series"></a>NV-serien

De virtuella datorerna NV-serien drivs av [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-kort och NVIDIA rutnät teknik för skrivbordet snabbare program och virtuella skrivbord där kunder kan visualisera data- eller simulering. Användare kan visualisera sina grafik beräkningsintensiva arbetsflöden för att hämta kapacitet överlägsen grafik och dessutom köra enkel precision arbetsbelastningar som till exempel kodning och återgivning NV instanser. 

Varje GPU i NV instanser levereras med en licens för RUTNÄTET. Denna licens ger dig möjlighet att använda en NV-instans som en virtuell dator för en enskild användare eller 25 samtidiga användare kan ansluta till den virtuella datorn för ett scenario med virtuella program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.

 
