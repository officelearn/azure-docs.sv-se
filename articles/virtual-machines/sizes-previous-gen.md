---
title: Azure Linux VM storlekar - tidigare generationer | Microsoft-dokument
description: Visar en lista över tidigare generationer av storlekar som är tillgängliga för virtuella Linux-datorer i Azure. Visar information om antalet virtuella processorer, datadiskar och nätverkskort samt lagringsdataflöde och nätverksbandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 6cf43df756e9bed0438169c9c01b868653d84b57
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985736"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Tidigare generationer av storlekar på virtuella datorer

Det här avsnittet innehåller information om tidigare generationer av storlekar på virtuella datorer. Dessa storlekar kan fortfarande användas, men det finns nyare generationer tillgängliga.

## <a name="f-series"></a>F-serien

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

ACU: 210–250

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3 000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6 000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12 000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24 000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48 000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs-serien <sup>1</sup>

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

ACU: 210–250

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

<sup>1</sup> Det maximala diskdataflödet (IOPS eller MBps) som är möjligt med en virtuell dator i Fs-serien kan begränsas av antalet, storleken och stripingen för de anslutna diskarna.  Mer information finns i design för hög prestanda för [Windows](windows/premium-storage-performance.md) eller [Linux](linux/premium-storage-performance.md).  


## <a name="nvv2-series"></a>NVv2-serien

**Nyare storlek rekommendation:** [NVv3-serien](nvv3-series.md)

De virtuella datorerna i NVv2-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU:er och NVIDIA GRID-teknik med Intel Broadwell-processorer. Dessa virtuella datorer är inriktade på GPU-accelererade grafikprogram och virtuella skrivbord där kunderna vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision, till exempel kodning och rendering. NVv2 virtuella datorer stöder Premium Storage och levereras med dubbelt systemminne (RAM) jämfört med sin föregångare NV-serien.  

Varje GPU i NVv2-instanser levereras med en GRID-licens. Den här licensen ger dig flexibiliteten att använda en NV-instans som en virtuell arbetsstation för en enskild användare, eller så kan 25 samtidiga användare ansluta till den virtuella datorn för ett virtuellt programscenario.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Äldre generationer av storlekar på virtuella datorer

Det här avsnittet innehåller information om äldre generationer av storlekar på virtuella datorer. Dessa storlekar stöds fortfarande men får inte ytterligare kapacitet. Det finns nyare eller alternativa storlekar som är allmänt tillgängliga. Se [storlekar för virtuella Linux-datorer i Azure](linux/sizes.md) för att välja de VM-storlekar som bäst passar dina behov.  

Mer information om hur du ändrar storlek på en Virtuell Linux-dator finns i [Ändra storlek på en Virtuell Linux-dator](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Nyare storlek rekommendation:** [Av2-serien](av2-series.md)

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Storlekarna på den grundläggande nivån är främst avsedda för utvecklingsarbetsbelastningar och andra program som inte kräver belastningsutjämning, automatisk skalning eller minnesintensiva virtuella datorer.

| Storlek – Storlek\namn | Virtuell processor | Minne|Nätverkskort (max.)| Högsta temporär diskstorlek | Max. datadiskar (1023 GB vardera)| Max. IOPS (300 per disk) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 × 300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2 × 300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4 × 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 × 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 × 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 med CLI och PowerShell

I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge

### <a name="a-series"></a>A-serien  

**Nyare storlek rekommendation:** [Av2-serien](av2-series.md)

ACU: 50–100

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| &nbsp;Standard_A0<sup>1</sup> | 1 | 0.768 | 20 | 1 | 1 × 500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2 × 500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4 × 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 × 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 × 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 × 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 × 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 × 500 | 4/2000 |

<sup>1</sup> A0-storleken är övertecknad på den fysiska hårdvaran. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser  

**Nyare storlek rekommendation:** [Av2-serien](av2-series.md)

ACU: 225

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort|
|---|---|---|---|---|---|---|
| &nbsp;Standard_A8<sup>1</sup> | 8  | 56  | 382 | 32 | 32 × 500 | 2 |
| &nbsp;Standard_A9<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 × 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1.</sup> För MPI-program aktiveras dedikerade RDMA-backend-nätverk av FDR InfiniBand-nätverk, som ger extremt låg latens och hög bandbredd.  

> [!NOTE]
> De virtuella datorerna A8 – A11 är planerade att gå i pension den 3/2021. Mer information finns i [HPC:s migreringsguide](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-serien  

**Nyare storlek rekommendation:** [Dv3-serien](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3 000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6 000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12 000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24 000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM-familjen kan köras på en av följande processorer: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-serien - minne optimerad  

**Nyare storlek rekommendation:** [Dv3-serien](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6 000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12 000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24 000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48 000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM-familjen kan köras på en av följande processorer: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Förhandsgranskning: DC-serien

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

DC-serien använder den senaste generationen av 3,7 GHz Intel XEON E-2176G-processor med SGX-teknik, och med Intel Turbo Boost-tekniken kan gå upp till 4,7 GHz. 

| Storlek          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Max nätverkskort / Förväntad nätverksbandbredd (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2/1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Virtuella datorer i DC-serien är [virtuella generationer i 2 virtuella datorer](./linux/generation-2.md#creating-a-generation-2-vm) och stöder `Gen2` endast avbildningar.


### <a name="ds-series"></a>DS-serien  

**Nyare storlek rekommendation:** [Dsv3-serien](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM-familjen kan köras på en av följande processorer: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-serien - minnesoptimerat  

**Nyare storlek rekommendation:** [Dsv3-serien](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> Det maximala diskgenomströmningsflödet (IOPS eller MBps) som är möjligt med en VM-serie i DS-serien kan begränsas av antalet, storleken och stripingen för de anslutna diskarna.  Mer information finns i design för hög prestanda för [Windows](windows/premium-storage-performance.md) eller [Linux](linux/premium-storage-performance.md).
<sup>2</sup> VM-familjen kan köras på en av följande processorer: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-serien

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.

Ls-serien stöder inte skapandet av en lokal cache för att öka IOPS som kan uppnås av varaktiga datadiskar. Det höga dataflödet och IOPS på den lokala disken gör virtuella datorer i Ls-serien idealiska för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att uppnå uthållighet i händelse av fel på en enda virtuell dator.

ACU: 180–240

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne (GiB) | Temp lagring (GiB) | Maximalt antal datadiskar | Max temp lagringsdataflöde (IOPS/MBps) | Max oankopplat diskdataflöde (IOPS/MBps) | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| &nbsp;Standard_L32s<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Det maximala diskdataflödet med virtuella datorer i Ls-serien kan begränsas av antalet, storleken och stripingen för alla anslutna diskar. Mer information finns i design för hög prestanda för [Windows](windows/premium-storage-performance.md) eller [Linux](linux/premium-storage-performance.md).

<sup>1</sup> Instans är isolerad till hårdvara tillägnad en enda kund.

### <a name="gs-series"></a>GS-serien

ACU: 180 - 240 <sup>1</sup>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| &nbsp;Standard_GS4<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| &nbsp;Standard_GS5<sup>&nbsp;2, 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> Det maximala diskgenomströmningsflödet (IOPS eller MBps) som är möjligt med en VM-serie i GS-serien kan begränsas av antalet, storleken och stripingen på de anslutna diskarna. Mer information finns i design för hög prestanda för [Windows](windows/premium-storage-performance.md) eller [Linux](linux/premium-storage-performance.md).

<sup>2</sup> Instansen är isolerad till maskinvara som är avsedd för en enskild kund.

<sup>3</sup> Begränsade kärnstorlekar tillgängliga.

<br>

### <a name="g-series"></a>G-serien

ACU: 180–240

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6 000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12 000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24 000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48 000/750/375 | 64/64x500 | 8/16000 |
| &nbsp;Standard_G5<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> Instans är isolerad till hårdvara tillägnad en enda kund.
<br>

## <a name="nv-series"></a>NV-serien
**Nyare storlek rekommendation:** [NVv3-serien](nvv3-series.md) och [NVv4-serien](nvv4-series.md)

De virtuella datorerna i NV-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU:er och NVIDIA GRID-teknik för stationära accelererade program och virtuella skrivbord där kunderna kan visualisera sina data eller simuleringar. Användare kan visualisera sina grafikintensiva arbetsflöden på NV-instanserna för att få överlägsen grafikkapacitet och dessutom köra arbetsbelastningar med enkel precision, till exempel kodning och rendering. NV-serien virtuella datorer drivs också av Intel Xeon E5-2690 v3 (Haswell) processorer.

Varje GPU i NV-instanser levereras med en GRID-licens. Den här licensen ger dig flexibiliteten att använda en NV-instans som en virtuell arbetsstation för en enskild användare, eller så kan 25 samtidiga användare ansluta till den virtuella datorn för ett virtuellt programscenario.

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.
<br>

## <a name="other-sizes"></a>Andra storlekar

* [Generellt syfte](sizes-general.md)
* [Beräkningsoptimerad](sizes-compute.md)
* [Minnesoptimerad](sizes-memory.md)
* [Lagringsoptimerad](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Databehandling med höga prestanda](sizes-hpc.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
