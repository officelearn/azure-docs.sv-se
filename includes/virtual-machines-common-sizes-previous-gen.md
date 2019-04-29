---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: d0802cfcf05874044b6e116ba194c16a79f9d309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541610"
---
Det här avsnittet innehåller information om tidigare versioner av storlekar för virtuella datorer. Dessa storlekar kan fortfarande användas, men det finns nyare generationer. 

## <a name="f-series"></a>F-serien

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

ACU: 210 - 250

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS-serien <sup>1</sup>

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

ACU: 210 - 250

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2/1 500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4/3 000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8/6 000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8 / 12000 |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i Fs-serien kan begränsas antal, storlek och striping av de anslutna diskarna.  Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Ls-serien

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.

Ls-serien stöder inte skapandet av en lokal cache för att öka IOPS kan uppnås genom beständiga datadiskar. Högt dataflöde och IOPS för den lokala disken gör Ls-serien virtuella datorerna är perfekt för NoSQL, till exempel Apache Cassandra och MongoDB som replikerar data mellan flera virtuella datorer att uppnå beständighet i händelse av fel på en enskild virtuell dator.

ACU: 180-240

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds inte
 
| Storlek          | Virtuell processor | Minne (GiB) | Temporär lagring (GiB) | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring (IOPS / Mbit/s) | Maximalt icke cachelagrat diskgenomflöde (IOPS / Mbit/s) | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5,000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1,388 | 32 | 40,000 / 400 | 10,000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2,807 | 64 | 80,000 / 800 | 20,000 / 500 | 8 / 16,000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40,000 / 1,000     | 8 / 20,000 | 

Det maximala diskgenomflödet som är möjligt med virtuella datorer i Ls-serien kan vara begränsas av antal, storlek och striping av alla anslutna diskar. Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 med CLI och PowerShell

I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge
