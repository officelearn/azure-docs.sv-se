---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 09f379279a7247f87b9e0830414a5e4363f41cdb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467557"
---
Minnesoptimerade VM-storlekar erbjuder ett högt förhållande mellan minne och processor som är bra för Relations databas servrar, medel stora och stora cacheminnen och minnes intern analys. Den här artikeln innehåller information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för varje storlek i den här gruppen.

* Ev3-serien har Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz-processorer (Broadwell) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta generella arbets belastningar och tar Ev3 till anpassning med de flesta virtuella datorerna i de flesta andra moln.  Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras med flytten till hyperthreading.  Ev3 är en uppföljning av de virtuella datorerna med hög minnes storlek för D/Dv2-familjer.

* Eav4-serien och Easv4-serien använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache, vilket ökar alternativen för att köra de flesta minnesoptimerade arbets belastningar.  Eav4-serien och Easv4-serien har samma minnes-och diskkonfigurationer som Ev3 & Esv3-serien.

* Mv2-serien erbjuder det högsta antalet vCPU (upp till 416 virtuella processorer) och störst minne (upp till 8,19 TiB) för virtuella datorer i molnet. Serien är det perfekta valet för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.

* M-serien erbjuder ett högt antal vCPU (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3,8 TiB). Det är också idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-räkningar och stora mängder minne.

* Dv2-serien, G-serien och DSv2/GS-motsvarigheterna är idealiska för program som kräver snabbare virtuella processorer, bättre tillfälliga lagrings prestanda eller har högre minnes krav. De utgör en kraftfull kombination för många program i företagsklass.

* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Dv2-serien är cirka 35% snabbare än D-serien. Den körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) och med Intel Turbo Boost-tekniken 2,0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

* Azure Compute ger VM-storlekar som isoleras till en specifik maskinvarutyp och dedikerad till en enda kund.  Dessa VM-storlekar passar bäst för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som innehåller element som efterlevnad och regelkrav.  Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Se tabellerna med virtuella dator familjer nedan för de isolerade VM-alternativen.

## <a name="esv3-series"></a>Esv3-serien

ACU: 160-190 <sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

Instanser av ESv3-serien använder Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz-processorer (Broadwell) och kan uppnå 3,5 GHz med Intel Turbo Boost Technology 2,0 och använder Premium Storage. Ev3-serieinstanserna är idealiska för minnesintensiva företagsprogram.


| Storlek             | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200 / 48                                | 2/1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2/2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8/16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Esv3 för virtuell dator i serien Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsade kärn storlekar är tillgängliga.

<sup>3</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

## <a name="easv4-series"></a>Easv4-serien

ACU: 230-260

Premium Storage: stöds

Premium Storage caching: stöds

Easv4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz och använder Premium SSD. Easv4-seriens storlek är idealiska för minnes intensiva företags program.

| Storlek | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200 / 48|2/1 000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2/2 000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200 / 768|8/16 000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Dessa storlekar är i för hands version. Om du är intresse rad av att testa dessa större storlekar kan du registrera dig på [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Ev3-serien 

ACU: 160-190 <sup>1</sup>

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Instanser av Ev3-serien använder Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz-processorer (Broadwell) och kan uppnå 3,5 GHz med Intel Turbo Boost Technology 2,0. Ev3-serieinstanserna är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien. 


| Storlek            | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort/nätverksbandbredd |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000/46/23                                               | 2/1 000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000/93/46                                               | 2/2 000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000/750/375                                            | 8/16 000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96 000/1 000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3 för virtuell dator i serien Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsade kärn storlekar är tillgängliga.

<sup>3</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

## <a name="eav4-series"></a>Eav4-serien

ACU: 230-260

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Eav4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz och använder Premium SSD. Eav4-seriens storlek är idealiska för minnes intensiva företags program. Datadisklagring faktureras separat från virtuella datorer. Om du vill använda Premium SSD använder du Easv4-seriens storlekar. Pris-och debiterings mätare för Easv4-storlekar är samma som för Eav3-serien.

| Storlek | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3 000 / 46 / 23|2/1 000 |
| Standard\_e4a\_v4|4|32|100|8|6 000 / 93 / 46|2/2 000 |
| Standard\_E8a\_v4|8|64|200|16|12 000 / 187 / 93|4 / 4000 |
| Standard\_E16a\_v4|16|128|400|32|24 000 / 375 / 187|8 / 8000 |
| Standard\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standard\_E32a\_v4|32|256|800|32|48 000 / 750 / 375|8/16 000 |
| Standard\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standard\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standard\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Dessa storlekar är i för hands version.  Om du är intresse rad av att testa dessa större storlekar kan du registrera dig på [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Mv2-serien

ACU: 188-280<sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

Skrivningsaccelerator: [stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2-serien har högt data flöde, låg latens-plattform som körs på en Hyper-threadd Intel® Xeon® platina Intel Xeon-processor med en alla grund frekvens på 2,5 GHz och en maximal Turbo frekvens på 3,8 GHz. Alla storlekar för virtuella datorer i Mv2-serien kan använda både standard-och Premium-diskar. Mv2-seriens instanser är minnesoptimerade VM-storlekar som ger oöverträffade beräknings prestanda för att stödja stora minnes databaser och arbets belastningar, med ett högt förhållande mellan minne och CPU som är idealiskt för Relations databas servrar, stora cacheminnen och minnes intern tjänstprogrammet.

|Storlek | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8/16 000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8/16 000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> Mv2 för virtuell dator i serien Intel® Hyper-Threading-teknik

<sup>2</sup> virtuella datorer i Mv2-serien är endast generation 2. Om du använder Linux, se [stöd för virtuella datorer i generation 2 på Azure](../articles/virtual-machines/linux/generation-2.md) för instruktioner om hur du hittar och väljer en avbildning.

<sup>3</sup> för M416ms_v2-och M416s_v2 storlekarna noterar du att det bara finns ett första stöd för följande avbildning: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 för SAP-program."

## <a name="m-series"></a>M-serien 

ACU: 160-180 <sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

Storlekarna på M-serien baseras på Intel (R) Xeon (R) CPU-E7-8890 v3 @ 2,50 GHz   

Skrivningsaccelerator: [stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Storlek            | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4/2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000 / 200 (1587) | 10000/250 | 8/4 000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8/16 000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000/1000 | 8/16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000/1000 | 8/16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000/1000 | 8/16 000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000/1000 | 8/16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> M-seriens virtuella dator funktioner Intel® Hyper-Threading-teknik

<sup>2</sup> över 64 vCPU kräver ett av följande gäst operativ system: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7,3 eller Oracle Linux 7,3 med Lis 4.2.1.

<sup>3</sup> begränsade kärn storlekar är tillgängliga.

<sup>4</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

ACU: 210-250 <sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

DSv2-seriens storlekar körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell).

| Storlek | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Max antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2/1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4/3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8/6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000/960 |8/25000&nbsp;<sup>4</sup>

<sup>1</sup> det maximala disk data flödet (IOPS eller Mbit/s) möjligt med en virtuell dator i DSv2-serien kan begränsas av antalet, storlek och striping av de anslutna diskarna.  Mer information finns i [utforma för hög prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> -instansen är isolerad till den Intel Haswell-baserade maskin varan och är dedikerad till en enda kund.  
<sup>3</sup> begränsade kärn storlekar är tillgängliga.  
<sup>4</sup> 25000 Mbit/s med accelererat nätverk. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-serien 11-15

ACU: 210–250

Premium Storage: stöds inte

Premium Storage caching: stöds inte

DSv2-seriens storlekar körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell).

| Storlek              | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60 000 / 937 / 468                                        | 64/64x500                       | 8/25000&nbsp;<sup>2</sup> |

<sup>1</sup> instans är isolerad till maskin vara som är dedikerad till en enda kund.  
<sup>2</sup> 25000 Mbit/s med accelererat nätverk. 
