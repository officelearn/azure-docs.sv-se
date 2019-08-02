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
ms.openlocfilehash: cfffc29a467a89416964564b9c55a73cbf77377d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601385"
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

## <a name="ls-series"></a>Ls-serien

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.

LS-serien stöder inte att en lokal cache skapas för att öka IOPS som kan nås av varaktiga data diskar. Med det höga genom strömningen och IOPS av den lokala disken kan virtuella datorer i LS-serien vara idealiska för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att få beständighet i händelse av att en enskild virtuell dator Miss lyckas.

ACU 180-240

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds inte
 
| Size          | Virtuell processor | Minne (GiB) | Temp-lagring (GiB) | Maximalt antal datadiskar | Maximalt data flöde för temporärt lagrings utrymme (IOPS/Mbit/s) | Maximalt antal cachelagrade diskar (IOPS/Mbit/s) | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000 / 125  | 2/4 000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8 000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16 000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8/20 000 | 

Det maximala disk data flödet som är möjligt med virtuella datorer i LS-serien kan begränsas av antal, storlek och striping för anslutna diskar. Mer information finns i [utforma för hög prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instans är isolerad till maskin vara som är dedikerad till en enda kund.

## <a name="nvv2-series-preview"></a>NVv2-serien (för hands version)

**Nyare storleks rekommendation**: [NVv3-serien (för hands version)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

De virtuella datorerna i NVv2-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-och NVIDIA grid-teknik med Intel Broadwell-processorer. Dessa virtuella datorer är avsedda för GPU-accelererade grafik program och virtuella skriv bord där kunder vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision som kodning och rendering. NVv2 Virtual Machines stöder Premium Storage och levereras med två gånger system minnet (RAM) jämfört med dess föregående NV-serie.  

Varje GPU i NVv2-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 med CLI och PowerShell

I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge
