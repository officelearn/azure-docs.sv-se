---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 7984172c2b66f2b09e31c646b111e4b9d04fce2b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551657"
---
Minnesoptimerade erbjudande för VM-storlekar högt minne att CPU-förhållande som är utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser. Den här artikeln innehåller information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

* M-serien erbjuder högst antal virtuella processorer (upp till 128 virtuella processorer) och störst minne (upp till 3.8 TiB) för virtuella datorer i molnet.  Serien är det perfekta valet för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.

* Dv2-serien, G-serien och DSv2/GS-motsvarigheterna är idealiska för program som kräver snabbare virtuella processorer, bättre prestanda för temporär lagring eller har högre minneskrav. De utgör en kraftfull kombination för många program i företagsklass.

* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den är baserad på den senaste generationen 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz- eller E5-2673 v4 (Broadwell) 2,3 GHz-processorer och kan gå upp till 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

* Ev3-serien funktioner E5-2673 v4 (Broadwell) 2,3 GHz-processorn i en hyper-threaded konfiguration, vilket ger ett mer prisvärt för de flesta allmänt syfte arbetsbelastningar och föra Ev3 justeras mot allmänna virtuella datorer i de flesta andra moln.  Minne har expanderats (från 7 GiB/Vcpu(:er) för 8 GiB/vCPU) medan disk- och gränser har justerats på basis av per kärna för att anpassas till flytten till hypertrådning.  Ev3 är Följ upp till de extra minne storlekarna av D/Dv2-familjer.

* Azure Compute ger VM-storlekar som isoleras till en specifik maskinvarutyp och dedikerad till en enda kund.  Dessa VM-storlekar passar bäst för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som innehåller element som efterlevnad och regelkrav.  Kunder kan också välja att dela upp resurser på dessa isolerade virtuella datorer med hjälp av ytterligare [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Se tabellerna i VM-familjer nedan för ditt isolerade VM-alternativ.

## <a name="esv3-series"></a>Esv3-serien 

ACU: 160-190 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

Instanserna i ESv3-serien baseras på 2,3 GHz Intel XEON® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5 GHz med Intel Turbo Boost Technology 2.0 och använder premiumlagring. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.


| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4 000/32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8 000/64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16 000/128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32 000/256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40,000 / 320 (400)                                                    | 32,000 / 480                              | 8 / 10,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64 000/512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1 600)                                                   | 80 000/1 200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1 600)                                                   | 80 000/1 200                             | 8 / 30,000                             |


<sup>1</sup> Esv3-seriens Virtuella datorns funktion Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsad core-storlekar som är tillgängliga.

<sup>3</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.


## <a name="ev3-series"></a>Ev3-serien 

ACU: 160 - 190 <sup>1</sup>

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

Instanserna i Ev3-serien baseras på 2,3 GHz Intel XEON ® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5GHz med Intel Turbo Boost Technology 2.0. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien. 


| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000/46/23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000/93/46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000/187/93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000/375/187                                            | 8 / 8,000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10,000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000/750/375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30,000           |

<sup>1</sup> Ev3-serien Virtuella datorns funktion Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsad core-storlekar som är tillgängliga.

<sup>3</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.


## <a name="m-series"></a>M-serien 

ACU: 160-180 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

Write Accelerator:  [Stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10,000 / 100 (793)  | 5,000  / 125 | 4 / 2,000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20,000 / 200 (1,587) | 10,000 / 250 | 8 / 4,000 |
| Standard_M32ts | 32 | 192    | 1,024 | 32 | 40,000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M32ls | 32 | 256    | 1,024 | 32 | 40,000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40,000 / 400 (3,174) | 20,000 / 500 | 8 / 8,000 |
| Standard_M64s  | 64 | 1,024   | 2,048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard_M64ls  | 64 | 512    | 2,048 | 64 | 80,000 / 800 (6,348) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1,792 | 2,048 | 64 | 80,000 / 800 (6,348)| 40,000 / 1,000 | 8 / 16,000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2,048        | 4,096  | 64 | 160 000/1 600 (12 696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3,892  | 4,096 | 64 | 160 000/1 600 (12 696) | 80,000 / 2,000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80,000  / 800  (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80,000  / 800  (1,228) | 40,000 / 1,000 | 8 / 16,000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2,048 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32,000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80,000 / 2,000 | 8 / 32,000 |



<sup>1</sup> M-serien Virtuella funktionen Intel® Hyper-Threading Technology

<sup>2</sup> högst 64 virtuella processorer kräver något av följande gästoperativsystem som stöds: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7.3 eller Oracle Linux 7.3 med LIS 4.2.1.

<sup>3</sup> begränsad core-storlekar som är tillgängliga.

<sup>4</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>

## <a name="gs-series"></a>GS-serien 

ACU: 180 - 240 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2/2 000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2/4 000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4/8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8/16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8/20 000 |

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i GS-serien kan begränsas antal, storlek och striping av de anslutna diskarna. Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.

<sup>3</sup> begränsad core-storlekar som är tillgängliga.

<br>

## <a name="g-series"></a>G-serien

ACU: 180 - 240

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6 000 / 93 / 46                                           | 8 / 8 x 500                       | 2/2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12 000 / 187 / 93                                         | 16 / 16 x 500                       | 2/4 000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24 000 / 375 / 187                                        | 32 / 32 x 500                     | 4/8 000                |
| Standard_G4  | 16        | 224         | 3,072          | 48 000 / 750 / 375                                        | 64 / 64 x 500                     | 8/16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96 000 / 1500 / 750                                       | 64 / 64 x 500                     | 8/20 000           |

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>

## <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

ACU: 210 - 250 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2/1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4/3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8/6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i DSv2-serien kan begränsas antal, storlek och striping av de anslutna diskarna.  Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.  
<sup>3</sup> begränsad core-storlekar som är tillgängliga.  
<sup>4</sup> 25000 Mbit/s med Accelererat nätverk. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-serien 11-15

ACU: 210 - 250

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek              | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1,000          | 60 000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.  
<sup>2</sup> 25000 Mbit/s med Accelererat nätverk. 