---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 9e145bc3a6824100409a0f6215152cdf70ec6777
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501281"
---
Minnesoptimerade erbjudande för VM-storlekar högt minne att CPU-förhållande som är utmärkt för relationsdatabasservrar, mellanstora till stora cacheminnen och minnesinterna analyser. Den här artikeln innehåller information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

* Mv2-serien erbjuder högst antal virtuella processorer (upp till 208 virtuella processorer) och störst minne (upp till 5.7 TiB) för virtuella datorer i molnet. Serien är det perfekta valet för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.
 
* M-serien erbjuder ett antal hög virtuella processorer (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3.8 TiB). Det är också idealiskt för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.

* Dv2-serien, G-serien och DSv2/GS-motsvarigheterna är idealiska för program som kräver snabbare virtuella processorer, bättre prestanda för temporär lagring eller har högre minneskrav. De utgör en kraftfull kombination för många program i företagsklass.

* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den är baserad på den senaste generationen 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz- eller E5-2673 v4 (Broadwell) 2,3 GHz-processorer och kan gå upp till 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

* Ev3-serien funktioner E5-2673 v4 (Broadwell) 2,3 GHz-processorn i en hyper-threaded konfiguration, vilket ger ett mer prisvärt för de flesta allmänt syfte arbetsbelastningar och föra Ev3 justeras mot allmänna virtuella datorer i de flesta andra moln.  Minne har expanderats (från 7 GiB/Vcpu(:er) för 8 GiB/vCPU) medan disk- och gränser har justerats på basis av per kärna för att anpassas till flytten till hypertrådning.  Ev3 är Följ upp till de extra minne storlekarna av D/Dv2-familjer.

* Azure Compute ger VM-storlekar som isoleras till en specifik maskinvarutyp och dedikerad till en enda kund.  Dessa VM-storlekar passar bäst för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som innehåller element som efterlevnad och regelkrav.  Kunder kan också välja att dela upp resurser på dessa isolerade virtuella datorer med hjälp av ytterligare [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Se tabellerna i VM-familjer nedan för ditt isolerade VM-alternativ.

## <a name="esv3-series"></a>Esv3-serien 

ACU: 160-190 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

Instanserna i ESv3-serien baseras på 2,3 GHz Intel XEON® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5 GHz med Intel Turbo Boost Technology 2.0 och använder premiumlagring. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.


| Size             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2/1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2/2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8/16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Esv3-seriens Virtuella datorns funktion Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsad core-storlekar som är tillgängliga.

<sup>3</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.


## <a name="ev3-series"></a>Ev3-serien 

ACU: 160 - 190 <sup>1</sup>

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

Instanserna i Ev3-serien baseras på 2,3 GHz Intel XEON ® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5GHz med Intel Turbo Boost Technology 2.0. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien. 


| Size            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
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

<sup>1</sup> Ev3-serien Virtuella datorns funktion Intel® Hyper-Threading-teknik.

<sup>2</sup> begränsad core-storlekar som är tillgängliga.

<sup>3</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.


## <a name="mv2-series"></a>Mv2-serien

Premium-lagring: Stöds

Premium Storage cachelagring: Stöds

Write Accelerator: [Stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Den Mv2-serien funktioner med stora dataflöden och låg latens, mappa direkt lokal NVMe lagring som körs på en hyper-threaded Intel® Xeon® Platinum 8180 M 2.5GHz (Skylake)-processor med en alla kärnor grundläggande frekvensen för 2,5 GHz och en max turbo frekvensen för 3,8 GHz. Alla storlekar för virtuella datorer av Mv2-serien kan använda beständiga standard- och premium-diskar. Mv2-serieinstanserna är minnesoptimerade VM-storlekar som tillhandahåller oöverträffade prestanda för stöd för stora minnesinterna databaser och arbetsbelastningar med högt minne att CPU-förhållande som passar utmärkt för relationsdatabasservrar, stora cacheminnen och minnesinterna Analytics. 

|Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/16 000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/16 000 |

Mv2-serien Virtuella datorns funktion Intel® Hyper-Threading Technology  

<sup>1</sup> dessa stora virtuella datorer begär en av följande gästoperativsystem som stöds: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> Mv2-seriens virtuella datorer är generation 2. Om du använder Linux finns i följande avsnitt att hitta och välja en SUSE Linux-avbildning.

#### <a name="find-a-suse-image"></a>Hitta en SUSE-avbildning

Att välja en lämplig avbildning med SUSE Linux i Azure portal: 

1. I Azure-portalen väljer du **skapa en resurs** 
1. Sök efter ”SUSE SAP” 
1. SLES för SAP generation 2-avbildningar är tillgängliga som antingen betala per användning eller Använd din egen prenumeration (BYOS). I sökresultaten expanderar du kategorin önskad bild:

    * SUSE Linux Enterprise Server (SLES) för SAP
    * SUSE Linux Enterprise Server (SLES) för SAP (BYOS)
    
1. SUSE-avbildningar som är kompatibla med Mv2-serien har ett prefix med namnet `GEN2:`. Följande SUSE-avbildningar är tillgängliga för virtuella datorer Mv2-serien:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 för SAP-program
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 för SAP-program
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 för SAP-program (BYOS)
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 för SAP-program (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Välj en avbildning av SUSE via Azure CLI

Om du vill se en lista över tillgängliga SLES för SAP-avbildningen för virtuella datorer Mv2-serien kan använda följande [ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) kommando:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Kommandot returnerar de för närvarande tillgänglig Generation 2 virtuella datorer tillgängliga från SUSE för Mv2-seriens virtuella datorer. 

Exempel på utdata:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M-serien 

ACU: 160-180 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

Write Accelerator:  [Stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4/2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8/4 000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8/16 000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8/16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8/16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8/16 000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8/16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> M-serien Virtuella funktionen Intel® Hyper-Threading Technology

<sup>2</sup> högst 64 virtuella processorer kräver något av följande gästoperativsystem som stöds: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7.3 eller Oracle Linux 7.3 med LIS 4.2.1.

<sup>3</sup> begränsad core-storlekar som är tillgängliga.

<sup>4</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

ACU: 210 - 250 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2/1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4/3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8/6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i DSv2-serien kan begränsas antal, storlek och striping av de anslutna diskarna.  Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.  
<sup>3</sup> begränsad core-storlekar som är tillgängliga.  
<sup>4</sup> 25000 Mbit/s med Accelererat nätverk. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-serien 11-15

ACU: 210 - 250

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

| Size              | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60 000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.  
<sup>2</sup> 25000 Mbit/s med Accelererat nätverk. 
