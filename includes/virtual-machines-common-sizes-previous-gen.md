---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 632b23a726b8e006152662ef7814aa4b7ef887fc
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984566"
---
Det här avsnittet innehåller information om tidigare generationer av virtuella dator storlekar. De här storlekarna kan fortfarande användas, men det finns nya generationer som är tillgängliga. 

## <a name="f-series"></a>F-serien

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

ACU 210 – 250

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

| Size         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporärt lagring: IOPS/läsa Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48 000 / 750 / 375                                        | 64/64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS-serie <sup>1</sup>

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

ACU 210 – 250

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS/Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200 / 48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8000 / 64 (24) |6400 / 96 |2/1 500 |
| Standard_F4s |4 |8 |16 |16 |16000 / 128 (48) |12800 / 192 |4/3 000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8/6 000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200 / 768 |8 / 12000 |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

<sup>1</sup> det högsta antalet disk data flöde (IOPS eller Mbit/s) som är möjligt med en virtuell FS-serienhet kan begränsas av antal, storlek och striping av de anslutna diskarna.  Mer information finns i [utforma för hög prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="nvv2-series"></a>NVv2-serien

**Nyare storleks rekommendation**: [NVv3-serien](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

De virtuella datorerna i NVv2-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-och NVIDIA grid-teknik med Intel Broadwell-processorer. Dessa virtuella datorer är avsedda för GPU-accelererade grafik program och virtuella skriv bord där kunder vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision som kodning och rendering. NVv2 Virtual Machines stöder Premium Storage och levereras med två gånger system minnet (RAM) jämfört med dess föregående NV-serie.  

Varje GPU i NVv2-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |
