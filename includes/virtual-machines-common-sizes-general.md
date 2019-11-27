---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: 85429e67c5e02ef6eb9fe4ef76a5c7e1abaa3d69
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485456"
---
Generella storlekar för virtuella datorer ger balanserade förhållandet mellan processor och minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde för storlekar i den här grupperingen.

- [DC-serien](#dc-series) är en familj av virtuella datorer i Azure som kan hjälpa till att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i det offentliga molnet. De här datorernas backas av den senaste generationens Intel XEON E-2176G-processor på 3,7 GHz med SGX-teknik. Med Intel Turbo Boost Technology kan de här datorerna nå 4,7 GHz. DC-serien gör att kunder kan skapa säkra, enklavbaserade program som skyddar kod och data medan de används.

- De virtuella datorerna i AV2-serien kan distribueras på olika typer av maskin vara och processorer. Virtuella datorer i A-serien har CPU-prestanda och minneskonfigurationer som är bäst lämpade för arbetsbelastningar på ingångsnivån som utveckling och testning. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.

  Exempel på användnings områden är utvecklings-och test servrar, webb servrar med låg trafik, små till medel stora databaser, koncept koncept och kod databaser.

- Dv2-serien, som är en uppföljning av den ursprungliga D-serien, har en kraftfullare processor och optimal processor-till-minnes-konfiguration som gör dem lämpliga för de flesta produktions arbets belastningar. Dv2-serien är cirka 35% snabbare än D-serien. Dv2-serien körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- Dv3-serien körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) i en Hyper-threaded-konfiguration, vilket ger ett bättre värde för de flesta allmänna syfte med arbets belastningar.  Minnet har utökats (från ~ 3,5 GiB/vCPU till 4 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras efter flytta till hyperthreading.  Dv3-serien har inte längre hög minnes storlek för virtuella datorer i D/Dv2-serien, de har flyttats till den minnesoptimerade Ev3-serien för [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) och [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Exempel på användnings områden i D-serien är program i företags klass, Relations databaser, minnes intern cachelagring och analys.

- Dav4-serien och Dasv4-serien är nya storlekar som använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 GB L3-cache DEDIKERA 8 GB av den L3-cachen till varje 8 kärnor som ökar kund alternativen för att köra deras allmänna syfte med arbets belastningar. Dav4-serien och Dasv4-serien har samma minnes-och diskkonfigurationer som D & Dsv3-serien.
  
## <a name="b-series"></a>B-serien

Premium Storage: stöds

Premium Storage caching: stöds inte

De virtuella datorerna i B-serien är idealiska för arbets belastningar som inte behöver PROCESSORns fulla prestanda kontinuerligt, t. ex. webb servrar, små databaser och utvecklings-och test miljöer. Dessa arbets belastningar har vanligt vis höga prestanda krav. B-serien ger kunderna möjlighet att köpa en VM-storlek med en pris medveten bas linje prestanda som gör det möjligt för den virtuella dator instansen att bygga krediter när den virtuella datorn använder mindre än dess grundläggande prestanda. När den virtuella datorn har ackumulerat kredit kan den virtuella datorn överföras över den virtuella datorns bas linje med upp till 100% av CPU: n när programmet kräver högre CPU-prestanda.

Exempel på användnings områden är utvecklings-och test servrar, webb servrar med låg trafik, små databaser, mikrotjänster, servrar för proof-of-Concepts, build-servrar.


| Storlek             | Virtuell processor  | Minne: GiB | Temporär lagring (SSD) GiB | Bas processor prestanda för virtuell dator | Högsta CPU-prestanda för virtuell dator | Inledande krediter | Krediter i bank/timme | Högsta antal krediter | Maximalt antal datadiskar | Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40 %                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90 %                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4 320 / 50                                 | 4 320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1 200 %                   | 360                   | 121                 | 2 909           | 16                                     | 6480/75                                 | 4 320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1 600 %                   | 480                   | 162                 | 3 888           | 32                                     | 8640 / 100                                 | 4 320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2 000 %                   | 600                   | 203                 | 4 860           | 32                                     | 10800 / 125                                 | 4 320 / 50                                 | 8  |

<sup>1</sup> B1ls stöds bara på Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3-serie <sup>1</sup>

ACU: 160–190

Premium Storage: stöds

Premium Storage caching: stöds

Dsv3-seriens storlekar körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0 och Använd Premium Storage. Storlekarna i Dsv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.


| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200 / 48                                | 2/1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2/2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8/16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3 för virtuell dator i serien Intel® Hyper-Threading-teknik

## <a name="dasv4-series"></a>Dasv4-serien

ACU: 230-260

Premium Storage: stöds

Premium Storage caching: stöds

Dasv4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz och använder Premium SSD. Storlekarna i Dasv4-serien erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktions arbets belastningar.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200 / 48|2/1 000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2/2 000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200 / 768|8/16 000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Dessa storlekar är i för hands version.  Om du är intresse rad av att testa dessa större storlekar kan du registrera dig på [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Dv3-serie <sup>1</sup>

ACU: 160–190

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Dv3-seriens storlekar körs på Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0. Storlekarna i Dv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du Dsv3-storlekarna. Pris- och debiteringsmätarna för Dsv3-storlekar är samma som för Dv3-serien. 


| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Högsta antal nätverkskort/nätverks bandbredd (Mbit/s) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000/46/23                                               | 2/1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000/93/46                                               | 2/2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48 000/750/375                                            | 8/16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96 000/1 000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30000                   |

<sup>1</sup> dv3 för virtuell dator i serien Intel® Hyper-Threading-teknik

## <a name="dav4-series"></a>Dav4-serien

ACU: 230-260

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Dav4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz. Storlekarna i Dav4-serien erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktions arbets belastningar. Datadisklagring faktureras separat från virtuella datorer. Om du vill använda Premium SSD använder du Dasv4-storlekarna. Pris-och debiterings mätare för Dasv4-storlekar är samma som för Dav4-serien.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3 000 / 46 / 23   | 2/1 000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6 000 / 93 / 46   | 2/2 000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12 000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24 000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48 000 / 750 / 375 |8/16 000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Dessa storlekar är i för hands version.  Om du är intresse rad av att testa dessa större storlekar kan du registrera dig på [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>DSv2-serien

ACU: 210–250

Premium Storage: stöds

Premium Storage caching: stöds

DSv2-seriens storlekar körs på Intel® Xeon® 8171M 2.1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0 och Använd Premium Storage.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000/32 (43) |3200 / 48 |2/750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2/1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4/3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8/6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-serien

ACU: 210–250

Premium Storage: stöds inte

Premium Storage caching: stöds inte

DSv2-seriens storlekar körs på Intel® Xeon® 8171M 2.1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0.

| Storlek           | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar | Data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 4              | 4 × 500            | 2/750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 8              | 8 × 500            | 2/1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 16             | 16 × 500           | 4/3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 32             | 32 × 500           | 8/6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48 000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-serien

ACU: 100

Premium Storage: stöds inte

Premium Storage caching: stöds inte

AV2-seriens storlekar körs på Intel® Xeon® 8171M 2.1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2,0 och Använd Premium Storage.

| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1 000 / 20 / 10                                           | 2 / 2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |

## <a name="dc-series"></a>DC-serien

Premium Storage: stöds

Premium Storage caching: stöds



| Storlek          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200 /48                                  | 2/1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
