---
title: SAP HANA Azure VM-lagringskonfigurationer | Microsoft Docs
description: Storage rekommendationer för virtuella datorer som har SAP HANA som distribuerats i dem.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735518"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Lagringskonfigurationer för SAP HANA Azure VM

Azure tillhandahåller olika typer av lagring som är lämpliga för virtuella Azure-datorer som kör SAP HANA. Vilka typer av Azure storage som för SAP HANA distributioner lista som: 

- Standard SSD-hårddiskar (SSD)
- Premium solid state-hårddiskar (SSD)
- Ultra SSD i offentlig förhandsversion och har ännu inte stöds med SAP-program i produktion

Mer information om dessa disktyper, finns i artikeln [Välj en disktyp av](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure erbjuder två metoder för distribution för virtuella hårddiskar på Azure Standard och Premium Storage. Om det övergripande scenariot tillåter dra nytta av [Azure hanterade disk](https://azure.microsoft.com/services/managed-disks/) distributioner. 

En lista över lagringstyper och deras serviceavtal i IOPS och lagring dataflöde, granska de [Azure-dokumentationen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

**Rekommendation: Använda Azure Premium Storage tillsammans med Azure Write Accelerator och använda Azure Managed Disks för distribution**

I den lokala världen hade du sällan bryr dig om i/o-undersystem och dess funktioner. Anledning till var att leverantören av enheten krävs för att se till att de minsta krav är uppfyllda för SAP HANA. När du skapar Azure-infrastrukturen själv, bör du vara medveten om några av dessa krav. Några av de lägsta dataflödesegenskaper som uppmanas vilket resulterar i att behöva:

- Aktivera Läs/Skriv på **/hana/log** 250 MB/sek med 1 MB i/o-storlekar
- Aktivera Läs aktiviteten hos minst 400 MB/sekund för **/hana/data** för 16 MB och 64 MB i/o-storlekar
- Aktivera skrivningsaktiviteter minst 250 MB/sek för **/hana/data** med 16 MB och 64 MB i/o-storlekar

Svarstiden är viktigt för DBMS system, beroende på den låga storage även när DBMS, t.ex. SAP HANA, behålla data i minnet. Den kritiska vägen i storage är vanligtvis runt transaction log skrivningar av DBMS system. Men även operations skriva lagringspunkter eller läser in data i minnet när kraschåterställning kan vara avgörande. Det är därför **obligatorisk** att utnyttja Azure Premium-diskar för **/hana/data** och **/hana/log** volymer. För att uppnå lägsta dataflödet för **/hana/log** och **/hana/data** efter behov av SAP, du behöver för att skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar. Och Använd RAID-volymer som **/hana/data** och **/hana/log** volymer. 

**Rekommendation: Eftersom stripe-storlekar för RAID 0 rekommendationen är att använda:**

- 64 KB eller 128 KB för   **/hana/data**
- 32 KB för   **/hana/log**

> [!NOTE]
> Du behöver inte konfigurera någon redundansnivå med RAID-volymer eftersom Azure Premium och Standard-lagring att tre avbildningar av en virtuell Hårddisk. Användningen av en RAID-volym är helt och hållet att konfigurera volymer som ger tillräcklig i/o-dataflöde.

Kan sparas ett antal virtuella Azure-hårddiskar under en RAID är ackumulerande från en sida för IOPS och lagring dataflöde. Så om du placerar en RAID 0 över 3 x P30 Azure Premium Storage-diskar, ger den dig tre gånger IOPS och tre gånger kapaciteten för lagring för en enskild Azure Premium Storage P30-disk.

Cachelagring rekommendationerna nedan förutsatt att i/o-egenskaper för SAP HANA listan som:

- Det finns nästan alla skrivskyddade arbetsbelastningar mot HANA datafiler. Undantagen är stora storlekar I/o efter omstart av HANA-instans eller när data läses in i HANA. Ett annat fall av större läsa I/o mot datafiler kan vara HANA säkerhetskopior av databasen. Därmed måste Läs cachelagring huvudsakligen ingen visas meningsfull sedan i de flesta fall, alla datavolymer i filen läsas helt.
- Skriva mot datafilerna som uppstår i toppar baserat av HANA lagringspunkter och HANA kraschåterställning. Skriva lagringspunkter är asynkron och innehar inte upp alla användartransaktioner. Skrivdata under kraschåterställning är prestanda som är viktiga för att få systemet svarar snabbt igen. Kraschåterställning bör dock i stället undantagsfall
- Det är nästan alla läsningar från HANA gör om filerna. Undantagen är stora I/o när du genomför säkerhetskopieringar av transaktionsloggen kan kraschåterställning, eller i fasen omstart av en HANA-instans.  
- Största belastningen mot SAP HANA gör om loggfilen är skrivningar. Beroende på vilken typ av arbetsbelastning, kan det ha I/o som är så litet som 4 KB eller i andra fall i/o-storlekar på 1 MB eller mer. Skriva svarstid mot SAP HANA gör om loggen är prestanda som är viktiga.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

**Rekommendation: Till följd av dessa observerade i/o-mönster av SAP HANA anges cachelagring för olika volymer som använder Azure Premium Storage som:**

- **data/hana/** -ingen cachelagring
- **/ hana/log** – ingen cachelagring - undantag för M-serien (se senare i det här dokumentet)
- **/ hana/delade** – Läs cachelagring


Tänk också övergripande VM-i/o-dataflöde när du ändrar storlek på eller bestämmer dig för en virtuell dator. Den totala VM genomflödet dokumenteras i artikeln [storlekar för virtuella datorer för den minnesoptimerade](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Scheduler för Linux-i/o-läge
Linux har flera olika i/o-schemaläggning lägen. Vanlig rekommendation via Linux leverantörer och SAP är att konfigurera om läge för i/o-Schemaläggaren för diskvolymer från den **cfq** läge för att den **noop** läge. Information om refereras till i [SAP Obs! #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösning för produktion med Azure Write Accelerator för virtuella datorer i Azure M-serien
Azure Write Accelerator är en funktion som hämtar distribueras för Azure virtuella datorer i M-serien exklusivt. Som namnet tillstånd, är syftet med funktionen för att förbättra i/o-svarstiden för skrivningar Azure Premium Storage. För SAP HANA, Write Accelerator ska användas mot den **/hana/log** endast volymen. Därför kan den **/hana/data** och **/hana/log** är separata volymer med stöd för Azure Write Accelerator den **/hana/log** endast volymen. 

> [!IMPORTANT]
> SAP HANA-certifiering för virtuella datorer i Azure M-serien är enbart med Azure Write Accelerator för den **/hana/log** volym. Därför Produktionsdistribution scenariot SAP HANA på virtuella datorer i Azure M-serien förväntas vara konfigurerad med Azure Write Accelerator för den **/hana/log** volym.  

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i produktionsscenarier den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Rekommendation: Rekommenderade konfigurationer för produktionsscenarier se ut:**

| SKU FÖR VIRTUELL DATOR | RAM | Max. VM I/O<br /> Dataflöde | / hana/data | / hana/log | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 giB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 giB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 giB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 giB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 giB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM-typer är inte har gjorts tillgängliga av Microsoft för allmänheten. Kontrollera om genomflödet för de olika föreslagna enheterna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändrar storlek på en volym med flera virtuella hårddiskar än listade ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator.

Azure Write Accelerator fungerar bara tillsammans med [Azure hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör den **/hana/log** volymen behöver distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage virtuella hårddiskar per virtuell dator som stöds av Azure Write Accelerator. De aktuella gränserna är:

- 16 virtuella hårddiskar för en virtuell dator M128xx och M416xx
- 8 virtuella hårddiskar för en virtuell dator M64xx och M208xx
- 4 virtuella hårddiskar för ett M32xx VM

Mer detaljerad information om hur du aktiverar Azure Write Accelerator finns i artikeln [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Write Accelerator finns i samma-dokumentationen.

**Rekommendation: Du måste använda Write Accelerator för diskar som utgör /hana/log volymen**


## <a name="cost-conscious-azure-storage-configuration"></a>Kostnad medvetna Azure Storage-konfiguration
I följande tabell visas en konfiguration av VM-typer som kunder använder också till SAP HANA på Azure Virtual Machines-värd. Det kan finnas vissa typer av virtuella datorer som inte uppfyller alla villkor för minsta lagring för SAP HANA eller stöds inte officiellt med SAP HANA av SAP. Men än så länge de virtuella datorerna visat sig att utföra bra för icke-produktionsscenarion. Den **/hana/data** och **/hana/log** kombineras till en volym. Användningen av Azure Write Accelerator kan därför bli en begränsning som IOPS.

> [!NOTE]
> Kontrollera om en viss typ av virtuell dator stöds för SAP HANA av SAP i scenarier med SAP som stöds i [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> En förändring från tidigare rekommendationer för en kostnad medvetna lösning är att flytta från [Azure Standard HDD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) att utföra en bättre och mer tillförlitlig [Azure Standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKU FÖR VIRTUELL DATOR | RAM | Max. VM I/O<br /> Dataflöde | / hana/data och/hana/log<br /> stripe används med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap | Hana/säkerhetskopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 giB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 giB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 giB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 giB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 giB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 giB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 giB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 giB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 giB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM-typer är inte har gjorts tillgängliga av Microsoft för allmänheten. Diskarna rekommenderas för mindre VM-typer med 3 x P20 oversize volymer om utrymme rekommendationerna enligt den [SAP TDI Storage White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men gjordes valet som visas i tabellen för att tillhandahålla tillräckligt med diskgenomflödet för SAP HANA. Om du behöver ändringar av den **/hana/backup** volymen, som har anpassat för att behålla säkerhetskopior som representerar två gånger minne volymen kan passa på att justera.   
Kontrollera om genomflödet för de olika föreslagna enheterna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**, du måste öka antalet VHD: er för Azure Premium-lagring. Ändrar storlek på en volym med flera virtuella hårddiskar än listade ökar IOPS och i/o-dataflöde inom ramen för typ av virtuell Azure-dator. 

> [!NOTE]
> Konfigurationer som angetts ovan inte skulle ha nytta av [Azure-dator med enkel VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) eftersom den använder en blandning av Azure Premium Storage och Azure standardlagring. Dock har markeringen valts för att optimera kostnaderna. Du skulle behöva väljer Premiumlagring för alla diskar ovan som listas som (Sxx) att VM-konfigurationen är kompatibla med Azure enkel VM-serviceavtalet för Azure standardlagring.


> [!NOTE]
> Disk konfigurationsrekommendationer anges inriktar minimikraven SAP uttrycker mot sin infrastruktur-providers. I riktiga distributioner och arbetsbelastningsscenarier påträffades situationer där de här rekommendationerna fortfarande gav inte tillräckligt med funktioner. Dessa kan finnas situationer där en kund krävs en snabbare inläsning av data efter en omstart av HANA eller där säkerhetskopiera konfigurationer krävs högre bandbredd till lagringen. Andra fall ingår **/hana/log** där 5000 IOPS inte är tillräcklig för den specifika arbetsbelastningen. Så baserat gör de här rekommendationerna som ett börjar peka och anpassa på kraven för arbetsbelastningen.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Azure Ultra SSD lagringskonfigurationen för SAP HANA
Microsoft håller på att Vi presenterar en ny Azure storage-typ kallas [Azure Ultra SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). Den stora skillnaden mellan Azure storage erbjuds hittills och Ultra SSD är att diskfunktioner inte är bunden till diskstorleken längre. Som kund kan du definiera dessa funktioner för Ultra SSD:

- Storleken på en disk som sträcker sig från 4 GiB till 65 536 GiB
- IOPS mellan 100 IOPS och 160 kB IOPS (maximalt beror på VM-typer)
- Genomflödet från 300 MB per sekund till 2 000 MB per sekund

Leta upp artikeln för information om [presenterar Ultra SSD – nästa generations teknik för Azure Disks (förhandsversion)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD ger dig möjlighet att definiera en enskild disk som uppfyller dina storlek, IOPS och disk dataflöde intervall. Istället för att använda logiska volymhanterare som LVM eller MDADM ovanpå Azure Premium Storage för att skapa volymer som uppfyller krav på IOPS och lagring dataflöde. Du kan köra en blandning av konfiguration mellan UltraSSD och Premiumlagring. Därför kan du begränsa användningen av UltraSSD prestanda kritiska /hana/data och /hana/log volymer och täcker andra volymer med Azure Premium Storage

| SKU FÖR VIRTUELL DATOR | RAM | Max. VM I/O<br /> Dataflöde | / hana/datavolym | / hana/data i/o-dataflöde | / hana/data IOPS | / hana/loggvolymen | / hana/i/o-dataflöde | / hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 250 GB | 500 Mbit/s | 7500 | 256 GB | 500 Mbit/s  | 2000 |
| M32ls | 256 GB | 500 MB/s | 300 GB | 500 Mbit/s | 7500 | 256 GB | 500 Mbit/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M64s | 1000 giB | 1 000 MB/s |  1 200 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M64ms | 1 750 giB | 1 000 MB/s | 2 100 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M128s | 2000 giB | 2 000 MB/s |2 400 GB | 1200 Mbit/s |9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M128ms | 3800 giB | 2 000 MB/s | 4800 GB | 1200 Mbit/s |9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M208s_v2 | 2850 giB | 1 000 MB/s | 3500 GB | 1000 Mbit/s | 9000 | 512 GB | 500 Mbit/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7200 GB | 1000 Mbit/s | 9000 | 512 GB | 500 Mbit/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7200 GB | 1500 M bit/s | 9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M416ms_v2 | 11400 giB | 2 000 MB/s | 14400 GB | 1500 Mbit/s | 9000 | 512 GB | 800 Mbit/s  | 2000 |   

M416xx_v2 VM-typer är inte har gjorts tillgängliga av Microsoft för allmänheten. De värden som anges är avsedda att vara en startpunkt och måste utvärderas mot de verkliga behoven. Fördelen med Azure Ultra SSD är att värdena för IOPS och dataflöde kan anpassas utan att behöva stänga den virtuella datorn eller stoppat arbetsbelastningen användas i systemet.   

> [!NOTE]
> Så här långt, ögonblicksbilder av lagring med UltraSSD lagring är inte tillgänglig. Detta hindrar användningen av ögonblicksbilder av Virtuella med Azure Backup-tjänster

## <a name="next-steps"></a>Nästa steg
Mer information finns i:

- [Guide för hög tillgänglighet för SAP HANA för Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).