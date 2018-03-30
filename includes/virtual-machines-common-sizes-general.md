---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 32d1330b60de139a1ac16c4d8d70f76873dbd4c2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
Generella VM-storlekar ange förhållandet mellan belastningsutjämnade CPU-minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

- Virtuella datorer i A-serien och Av2-serien kan distribueras på en rad olika maskinvarutyper och processorer. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.

- Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till virtuell processor”-förhållande och en Solid State-hårddisk (SSD) för den temporära disken. Mer information finns i inlägget om [nya storlekar för virtuella datorer i D-serien](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) i Azure-bloggen.

- Dv3-serien, Dv2-serien, en uppföljning till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den baseras på den senaste generationens 2,4 GHz Intel Xeon® E5-2673 v3-processor (Haswell) och kan uppnå 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- Storlekarna på den grundläggande nivån är främst avsedda för utvecklingsarbetsbelastningar och andra program som inte kräver belastningsutjämning, automatisk skalning eller minnesintensiva virtuella datorer.

## <a name="b-series"></a>B-serien

B-serien burstable VMs lämpar sig för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och test miljöer. Dessa arbetsbelastningar har vanligtvis burstable prestandakrav. B-serien ger dessa kunder möjligheten att köpa en VM-storlek med ett pris samvetsfrihet baslinje-prestanda som tillåter VM-instans att bygga upp krediter när den virtuella datorn mindre än dess grundläggande prestanda. När den virtuella datorn har ackumulerats kredit, kan den virtuella datorn burst ovanför baslinjen för den virtuella datorn med upp till 100% CPU när programmet kräver högre CPU-prestanda.


| Storlek             | Virtuell processor  | Minne: GiB | Lokal SSD: GiB | Grundläggande Perf av en kärna | Krediter till bank / timme | Max till bank krediter | Maximalt antal datadiskar | Max lokal disk perf: IOPS / Mbit/s | Max ej cachelagrade disk perf: IOPS / Mbit/s | Maximalt antal nätverkskort |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>Dsv3-serien <sup>1</sup>

ACU: 160–190

Dsv3-serien storlekar baseras på 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor eller den senaste 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)-processor som kan uppnå 3.5GHz med Intel Turbo förstärkningen Technology 2.0 och använda premium-lagring. Storlekarna i Dsv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.


| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4 000/32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8 000/64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16 000/128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32 000/256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64 000/512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80 000/1 200                              | 8 / 30,000                                               |

<sup>1</sup> Dsv3-serien VM funktion Intel® flertrådsteknik

## <a name="dv3-series-sup1sup"></a>Dv3-serien <sup>1</sup>

ACU: 160–190

Dv3-serien storlekar baseras på 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)-processor som kan uppnå 3.5GHz med Intel Turbo förstärkningen Technology 2.0. Storlekarna i Dv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du Dsv3-storlekarna. Pris- och debiteringsmätarna för Dsv3-storlekar är samma som för Dv3-serien. 


| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort/nätverksbandbredd |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000/46/23                                               | 2 / 1,000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000/93/46                                               | 2 / 2,000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000/187/93                                             | 4 / 4,000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000/375/187                                            | 8 / 8,000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48 000/750/375                                            | 8 / 16,000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96 000/1 000/500                                            | 8 / 30,000                             |

<sup>1</sup> Dv3-serien VM funktion Intel® flertrådsteknik

## <a name="dsv2-series"></a>DSv2-serien

ACU: 210–250

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 48 |2/750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 96 |2/1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 192 |4/3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 384 |8/6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64,000 / 512 (688) |51,200 / 768 |8 / 12000 |



## <a name="dv2-series"></a>Dv2-serien

ACU: 210–250

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3.5 |4 |50 |3 000 / 46 / 23 |4 / 4 x 500 |2/750 |
| Standard_D2_v2 |2 |7 |8 |100 |6 000 / 93 / 46 |8 / 8 x 500 |2/1 500 |
| Standard_D3_v2 |4 |14 |16 |200 |12 000 / 187 / 93 |16 / 16 x 500 |4/3 000 |
| Standard_D4_v2 |8 |28 |32 |400 |24 000 / 375 / 187  |32 / 32 x 500 |8/6 000 |
| Standard_D5_v2 |16 |56 |64 |800 |48 000 / 750 / 375 |64 / 64 x 500 |8 / 12000 |

<br>

## <a name="ds-series"></a>DS-serien

ACU: 160

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2/500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2/1 000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4/2 000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8/4 000 |

<br>

## <a name="d-series"></a>D-serien 

ACU: 160

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/500                 |
| Standard_D2  | 2         | 7           | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 000                     |
| Standard_D3  | 4         | 14          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/2 000                     |
| Standard_D4  | 8         | 28          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/4 000                     |

<br>


## <a name="av2-series"></a>Av2-serien

ACU: 100



| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1 000 / 20 / 10                                           | 2 / 2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |

<br>

## <a name="a-series"></a>A-serien

ACU: 50–100

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 <sup>1</sup> |1 |0.768 |20 |1 |1 × 500 |2/100 |
| Standard_A1 |1 |1.75 |70 |2 |2 × 500 |2/500  |
| Standard_A2 |2 |3.5 |135 |4 |4 × 500 |2/500 |
| Standard_A3 |4 |7 |285 |8 |8 × 500 |2/1 000 |
| Standard_A4 |8 |14 |605 |16 |16 × 500 |4/2 000 |
| Standard_A5 |2 |14 |135 |4 |4 × 500 |2/500 |
| Standard_A6 |4 |28 |285 |8 |8 × 500 |2/1 000 |
| Standard_A7 |8 |56 |605 |16 |16 × 500 |4/2 000 |
<br>

<sup>1</sup> i A0 ligger över prenumererade på fysisk maskinvara. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 med CLI och PowerShell
I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall 
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge

## <a name="basic-a"></a>Basic A

|Storlek – Storlek\namn | Virtuell processor |Minne|Nätverkskort (max.)|Högsta temporär diskstorlek |Max. datadiskar (1 023 GB var)|Max. IOPS (300 per disk)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 × 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 × 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 × 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 × 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 × 300|


Observera att antalet hårddiskar för klassiska virtuella datorer kan vara lägre än antalet diskar för virtuella datorer i Azure Resource Manager.
