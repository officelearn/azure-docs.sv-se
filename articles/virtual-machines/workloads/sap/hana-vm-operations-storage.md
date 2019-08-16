---
title: SAP HANA lagrings konfiguration för virtuella Azure-datorer | Microsoft Docs
description: Lagrings rekommendationer för virtuell dator som har SAP HANA distribuerats i dem.
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
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1b363c9da195794f6356539ffca46101edf431c2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533887"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA

Azure tillhandahåller olika typer av lagring som lämpar sig för virtuella Azure-datorer som kör SAP HANA. De Azure Storage-typer som kan övervägas för SAP HANA distributions listor som: 

- Standard SSD disk enheter (SSD)
- Premium solid state-hårddiskar (SSD)
- [Ultra disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Information om de här disk typerna finns i artikeln [Välj en disk typ](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure erbjuder två distributions metoder för virtuella hård diskar på Azure standard och Premium Storage. Om det övergripande scenariot tillåter kan du utnyttja [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/) distributioner. 

En lista över lagrings typer och deras service avtal i IOPS och lagrings data flöde finns i [Azure-dokumentationen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

**Rekommenderade Använd Azure Premium Storage tillsammans med Azure Skrivningsaccelerator och Använd Azure-Managed Disks för distribution**

I den lokala världen var du sällan intresse rad av i/O-undersystemen och dess funktioner. Orsaken var att den nödvändiga enhets leverantören för att se till att minimi kraven för lagring är uppfyllda för SAP HANA. När du skapar Azure-infrastrukturen själv bör du vara medveten om några av dessa krav. Några av de lägsta egenskaperna för data flödet som ställs in, vilket innebär att du behöver:

- Aktivera Läs-och skriv åtgärder på **/Hana/log** i 250 MB/SEK med 1 MB i/O-storlekar
- Aktivera Läs aktivitet på minst 400 MB/SEK för **/Hana/data** för 16 mb och 64 MB i/O-storlekar
- Aktivera Skriv aktivitet med minst 250 MB/SEK för **/Hana/data** med 16 mb och 64 MB i/O-storlekar

Med tanke på att låg latens svars tider är avgörande för DBMS-system, även om DBMS, som SAP HANA, behåller data i minnet. Den kritiska sökvägen i lagrings utrymmet ligger vanligt vis runt transaktions logg skrivningar i DBMS-systemen. Men även åtgärder som att skriva lagrings punkter eller läsa in data i minnet efter krasch återställning kan vara avgörande. Därför är det **obligatoriskt** att utnyttja Azure Premium-diskar för **/Hana/data** -och **/Hana/log** -volymer. För att uppnå det lägsta genomflödet av **/Hana/log** och **/Hana/data** som du vill av SAP måste du skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar. Och använder RAID-volymerna som **/Hana/data** -och **/Hana/log** -volymer. 

**Rekommenderade Som rand storlek för RAID 0 rekommenderar vi att rekommendationen används:**

- 64 KB eller 128 KB för **/Hana/data**
- 32 KB för **/Hana/log**

> [!NOTE]
> Du behöver inte konfigurera någon redundans nivå med RAID-volymer sedan Azure Premium och standard lagring behåller tre avbildningar av en virtuell hård disk. Användningen av en RAID-volym är enbart att konfigurera volymer som tillhandahåller tillräckligt I/O-genomflöde.

Ackumulerar ett antal Azure-VHD: er under en RAID-accumulative från en IOPS-och lagrings plats för lagring. Så om du sätter en RAID 0 över 3 x P30 Azure Premium Storage-diskar, bör det ge dig tre gånger IOPS och tre gånger lagrings data flödet för en enskild Azure Premium Storage P30-disk.

Rekommendationerna för cachelagring nedan förutsätter i/O-egenskaperna för SAP HANA listan, t. ex.:

- Det finns ingen eventuell Läs arbets belastning mot HANA-datafilerna. Undantag är stora I/o efter omstart av HANA-instansen eller när data har lästs in i HANA. Ett annat fall av större Läs-I/o mot datafiler kan vara HANA-databas säkerhets kopieringar. Som ett resultat för cachelagring av utdata är det oftast inte bra eftersom i de flesta fall måste alla data fil volymer läsas fullständigt.
- Det går att skriva till datafilerna i burst utifrån HANA-lagrings punkter och HANA-krasch återställning. Skrivning av lagrings punkter är asynkront och innehåller inga användar transaktioner. Att skriva data under krasch återställning är prestanda kritiska för att få systemet att svara snabbt och enkelt. Krasch återställning bör dock vara i stället för exceptionella situationer
- Det finns inga läsningar från HANA gör om-filerna. Undantag är stora I/o när du utför säkerhets kopieringar av transaktions loggar, krasch återställning eller i start fasen för en HANA-instans.  
- Huvud belastningen mot den SAP HANA göra om-logg filen skrivs. Beroende på arbets Belastningens natur kan du ha I/o-typ så litet som 4 KB eller i andra fall i/O-storlekar på 1 MB eller mer. Skriv fördröjning mot SAP HANA gör om loggen prestanda kritisk.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

**Rekommenderade Som ett resultat av dessa observerade I/O-mönster genom SAP HANA bör cachelagringen för de olika volymerna med Azure Premium Storage ställas in som:**

- **/Hana/data** – ingen cachelagring
- **/Hana/log** – ingen cachelagring – undantag för M-serien (se senare i det här dokumentet)
- **/Hana/Shared** – cachelagring för läsning


Behåll även det övergripande i/O-dataflödet för virtuella datorer i åtanke när du ändrar storlek på eller bestämmer för en virtuell dator. Det totala data flödet för VM-lagring dokumenteras i artikel [minnet optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Läge för I/O-Schemaläggaren I Linux
Linux har flera olika I/O-schemaläggnings lägen. Vanliga rekommendationer via Linux-leverantörer och SAP är att konfigurera om I/O Scheduler-läget för disk volymer från **CFQ** -läget till **Noop** -läge. Information finns i [SAP obs #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösning för produktions lagring med Azure Skrivningsaccelerator för Azure M-seriens virtuella datorer
Azure Skrivningsaccelerator är en funktion som bara samlas för virtuella datorer i Azure M-serien. Som namn tillstånd är syftet med funktionen att förbättra I/O-svars tiden för skrivningar mot Azure-Premium Storage. För SAP HANA ska Skrivningsaccelerator endast användas mot **/Hana/log** -volymen. Därför är **/Hana/data** och **/Hana/log** separata volymer med Azure Skrivningsaccelerator som bara stöder **/Hana/log** -volymen. 

> [!IMPORTANT]
> SAP HANA-certifiering för Azure M-seriens virtuella datorer är exklusivt med Azure Skrivningsaccelerator för **/Hana/log** -volymen. Därför förväntas produktions scenariot SAP HANA distributioner på virtuella datorer i Azure M-serien att konfigureras med Azure Skrivningsaccelerator för **/Hana/log** -volymen.  

> [!NOTE]
> För produktions scenarier kontrollerar du om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Rekommenderade De rekommenderade konfigurationerna för produktions scenarier ser ut så här:**

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /hana/data | /hana/log | /hana/shared | /root-volym | /usr/sap | Hana/säkerhets kopiering |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM-typer har ännu inte gjorts tillgängliga av Microsoft för allmänheten. Kontrol lera om lagrings data flödet för de olika föreslagna volymerna uppfyller den arbets belastning som du vill köra. Om arbets belastningen kräver högre volymer för **/Hana/data** och **/Hana/log**måste du öka antalet Azure Premium Storage virtuella hård diskar. Att ändra storlek på en volym med fler virtuella hård diskar än vad som anges i listan ökar IOPS-och I/O-genomflödet inom gränserna för den virtuella Azure-dator typen.

Azure-Skrivningsaccelerator fungerar bara tillsammans med [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör **/Hana/log** -volymen måste distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage virtuella hård diskar per virtuell dator som kan användas av Azure Skrivningsaccelerator. De aktuella gränserna är:

- 16 virtuella hård diskar för en M128xx-och M416xx VM
- 8 virtuella hård diskar för en M64xx-och M208xx-VM
- 4 virtuella hård diskar för en virtuell M32xx-dator

Mer detaljerad information om hur du aktiverar Azure Skrivningsaccelerator finns i artikeln [Skrivningsaccelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Skrivningsaccelerator finns i samma dokumentation.

**Rekommenderade Du måste använda Skrivningsaccelerator för diskar som utgör/Hana/log-volymen**


## <a name="cost-conscious-azure-storage-configuration"></a>Kostnads medveten Azure Storage konfiguration
I följande tabell visas en konfiguration av de VM-typer som kunderna också använder för att vara värdar för SAP HANA på virtuella Azure-datorer. Det kan finnas vissa VM-typer som kanske inte uppfyller alla minimi krav för lagring för SAP HANA eller som inte stöds av SAP HANA av SAP. Men det är så långt de virtuella datorerna verkade vara bra för scenarier med icke-produktion. **/Hana/data** och **/Hana/log** kombineras till en volym. Användningen av Azure Skrivningsaccelerator kan bli en begränsning i samband med IOPS.

> [!NOTE]
> För SAP-scenarier som stöds kan du kontrol lera om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> En förändring från tidigare rekommendationer för en kostnads medveten lösning är att gå från [Azure standard HDD diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) för att bättre prestera och mer pålitliga [Azure standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data och/Hana/log<br /> randigt med LVM eller MDADM | /hana/shared | /root-volym | /usr/sap | Hana/säkerhets kopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM-typer har ännu inte gjorts tillgängliga av Microsoft för allmänheten. Diskarna som rekommenderas för de mindre VM-typerna med 3 x P20 överdimensionerade volymerna för utrymmes rekommendationer enligt [fakta bladet för SAP TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Valet som visas i tabellen gjordes dock för att ge tillräckligt med disk data flöde för SAP HANA. Om du behöver ändra **/Hana/backup** -volymen, som har storleks Visa för att bevara säkerhets kopior som representerar två gånger minnes volymen, är det kostnads fritt att justera.   
Kontrol lera om lagrings data flödet för de olika föreslagna volymerna uppfyller den arbets belastning som du vill köra. Om arbets belastningen kräver högre volymer för **/Hana/data** och **/Hana/log**måste du öka antalet Azure Premium Storage virtuella hård diskar. Att ändra storlek på en volym med fler virtuella hård diskar än vad som anges i listan ökar IOPS-och I/O-genomflödet inom gränserna för den virtuella Azure-dator typen. 

> [!NOTE]
> Konfigurationerna ovan skulle inte ha nytta av den [virtuella Azure-datorns service avtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) för virtuell dator eftersom den använder en blandning av Azure Premium Storage och Azure standard Storage. Men valet valdes för att optimera kostnaderna. Du måste välja Premium Storage för alla diskar som anges som Azure standard Storage (SXX) för att göra VM-konfigurationen kompatibel med Azure Single VM service avtal.


> [!NOTE]
> De angivna disk konfigurations rekommendationerna är till för att uppfylla minimi kraven för SAP Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier påträffades situationer där dessa rekommendationer fortfarande inte tillhandahöll tillräckliga funktioner. Det kan vara situationer där en kund behövde en snabbare inläsning av data efter en HANA-omstart eller där säkerhets kopierings konfigurationerna krävde högre bandbredd för lagringen. Andra fall som ingår **/Hana/log** där 5000 IOPS inte var tillräckligt för den speciella arbets belastningen. Så ta med rekommendationerna som en start punkt och anpassa utifrån kraven för arbets belastningen.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguration av Azure Ultra disk Storage för SAP HANA
Microsoft håller på att lansera en ny Azure Storage-typ som kallas [Azure Ultra disk](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk). Den stora skillnaden mellan Azure Storage och Ultra disk är att disk funktionerna inte är kopplade till disk storleken längre. Som kund kan du definiera dessa funktioner för Ultra disk:

- Storlek på en disk som sträcker sig från 4 GiB till 65 536 GiB
- IOPS sträcker sig från 100 IOPS till 160K IOPS (maximalt beror på VM-typer)
- Lagrings data flöde från 300 MB/SEK till 2 000 MB/SEK

Ultra disk ger dig möjlighet att definiera en enskild disk som uppfyller din storlek, IOPS och disk data flödes intervallet. I stället för att använda logiska volym hanterare som LVM eller MDADM ovanpå Azure Premium Storage för att skapa volymer som uppfyller kraven för IOPS och data flöde. Du kan köra en konfigurations kombination mellan Ultra disk och Premium Storage. Det innebär att du kan begränsa användningen av Ultra disk till de prestanda kritiska/Hana/data-och/Hana/log-volymerna och de andra volymerna med Azure Premium Storage

> [!IMPORTANT]
> Ultra disk finns ännu inte i alla Azure-regioner och har ännu inte stöd för alla typer av virtuella datorer. Detaljerad information om hur Ultra disk är tillgängligt och vilka VM-familjer som stöds finns i artikeln [vilka disk typer som är tillgängliga i Azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk).

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data volym | /Hana/data I/O-genomflöde | /Hana/data IOPS | /Hana/log volym | /Hana/log I/O-genomflöde | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 Mbit/s | 7500 | 256 GB | 500 Mbit/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 Mbit/s | 7500 | 256 GB | 500 Mbit/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M64s | 1000 GiB | 1 000 MB/s |  1 200 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M64ms | 1750 GiB | 1 000 MB/s | 2100 GB | 500 Mbit/s | 7500 | 512 GB | 500 Mbit/s  | 2000 |
| M128s | 2000 GiB | 2 000 MB/s |2400 GB | 1200 Mbit/s |9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M128ms | 3800 GiB | 2 000 MB/s | 4800 GB | 1200 Mbit/s |9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M208s_v2 | 2850 GiB | 1 000 MB/s | 3500 GB | 1000 Mbit/s | 9000 | 512 GB | 500 Mbit/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7200 GB | 1000 Mbit/s | 9000 | 512 GB | 500 Mbit/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7200 GB | 1500 bit/s | 9000 | 512 GB | 800 Mbit/s  | 2000 | 
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 14400 GB | 1500 Mbit/s | 9000 | 512 GB | 800 Mbit/s  | 2000 |   

M416xx_v2 VM-typer har ännu inte gjorts tillgängliga av Microsoft för allmänheten. Värdena i listan är avsedda att vara en start punkt och måste utvärderas mot de verkliga kraven. Fördelen med Azure Ultra disk är att värdena för IOPS och data flöde kan anpassas utan att du behöver stänga av den virtuella datorn eller stoppa arbets belastningen som tillämpas på systemet.   

> [!NOTE]
> Lagrings ögonblicks bilder med Ultra disk Storage är hittills inte tillgängliga. Detta blockerar användningen av ögonblicks bilder av virtuella datorer med Azure Backup Services

## <a name="next-steps"></a>Nästa steg
Mer information finns i:

- [SAP HANA guide för hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).