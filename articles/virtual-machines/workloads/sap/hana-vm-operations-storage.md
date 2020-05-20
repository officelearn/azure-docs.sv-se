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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa3096f43952c047620b310412b27c434fc5fb06
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682600"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA

Azure tillhandahåller olika typer av lagring som lämpar sig för virtuella Azure-datorer som kör SAP HANA. **SAP HANA certifierade Azure Storage-typer** som kan övervägas för SAP HANA distributions listor som: 

- Azure-Premium SSD  
- [Ultradisk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Information om de här disk typerna finns i artikeln [Välj en disk typ](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure erbjuder två distributions metoder för virtuella hård diskar på Azure standard och Premium Storage. Om det övergripande scenariot tillåter kan du utnyttja [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/) distributioner. 

En lista över lagrings typer och deras service avtal i IOPS och lagrings data flöde finns i [Azure-dokumentationen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Oberoende av vilken typ av Azure-lagring som valts måste det fil system som används på lagrings platsen stödjas av SAP för det angivna operativ systemet och DBMS. [SAP support note #405827](https://launchpad.support.sap.com/#/notes/405827) listar de fil system som stöds för olika operativ system och databaser, inklusive SAP HANA. Detta gäller för alla volymer SAP HANA kan komma åt för att läsa och skriva för vilken aktivitet som än är. I synnerhet med NFS på Azure för SAP HANA, gäller ytterligare begränsningar för NFS-versioner som anges längre fram i den här artikeln 


Lägsta SAP HANA certifierade villkor för olika lagrings typer är: 

- Azure Premium SSD-/Hana/log krävs för att cachelagras med Azure [Skrivningsaccelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). /Hana/data-volymen kan placeras på Premium SSD utan Azure Skrivningsaccelerator eller på Ultra disk
- Azure Ultra disk minst för/Hana/log-volymen. /Hana/data-volymen kan placeras antingen på Premium SSD utan Azure Skrivningsaccelerator eller för att få snabbare omstart gånger Ultra disk
- **NFS v 4.1** -volymer ovanpå Azure NetApp Files för/Hana/log **och** /Hana/data. Volymen av/Hana/Shared kan använda NFS v3-eller NFS v 4.1-protokollet. NFS v 4.1-protokollet är obligatoriskt för/Hana/data-och/Hana/log-volymer.

Några av lagrings typerna kan kombineras. Det är till exempel möjligt att placera/Hana/data på Premium Storage och/Hana/log kan placeras på Ultra disk Storage för att få en nödvändig låg latens. Om du använder en NFS v 4.1-volym baserat på ANF för/Hana/data måste du dock använda en annan NFS v 4.1-volym baserat på ANF för/Hana/log. Att använda NFS ovanpå ANF för en av volymerna (t. ex./Hana/data) och Azure Premium Storage eller Ultra disk för den andra volymen (t. ex./Hana/log) **stöds inte**.

I den lokala världen var du sällan medveten om I/O-undersystemen och dess funktioner. Orsaken var att den nödvändiga enhets leverantören för att se till att minimi kraven för lagring är uppfyllda för SAP HANA. När du skapar Azure-infrastrukturen själv bör du vara medveten om några av dessa SAP-utfärdade krav. Några av de minsta data flödes egenskaper som krävs från SAP, vilket leder till att du behöver:

- Aktivera Läs-och skriv åtgärder på **/Hana/log** i 250 MB/SEK med 1 MB i/O-storlekar
- Aktivera Läs aktivitet på minst 400 MB/SEK för **/Hana/data** för 16 mb och 64 MB i/O-storlekar
- Aktivera Skriv aktivitet med minst 250 MB/SEK för **/Hana/data** med 16 mb och 64 MB i/O-storlekar

Med tanke på att låg latens svars tider är avgörande för DBMS-system, även om DBMS, som SAP HANA, behåller data i minnet. Den kritiska sökvägen i lagrings utrymmet ligger vanligt vis runt transaktions logg skrivningar i DBMS-systemen. Men även åtgärder som att skriva lagrings punkter eller läsa in data i minnet efter krasch återställning kan vara avgörande. Därför är det **obligatoriskt** att utnyttja Azure Premium-diskar för **/Hana/data** -och **/Hana/log** -volymer. För att uppnå det lägsta genomflödet av **/Hana/log** och **/Hana/data** som du vill av SAP måste du skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar. Och använder RAID-volymerna som **/Hana/data** -och **/Hana/log** -volymer. 

> [!IMPORTANT]
>De tre SAP HANA fil grupperna/data,/log och/Shared får inte placeras i en standard-eller rot volym grupp.  Vi rekommenderar starkt att du följer rikt linjerna för Linux-leverantörer som vanligt vis skapar enskilda volym grupper för/data,/log och/Shared.

**Rekommendation: som stripe-storlekar för RAID 0 rekommenderar vi att rekommendationen används:**

- 256 KB för **/Hana/data**
- 32 KB för **/Hana/log**

> [!IMPORTANT]
> Stripe-storleken för/Hana/data har ändrats från tidigare rekommendationer som anropar för 64 KB eller 128 KB till 256 KB baserat på kund upplevelser med nyare Linux-versioner. Storleken på 256 KB ger något bättre prestanda

> [!NOTE]
> Du behöver inte konfigurera någon redundans nivå med RAID-volymer sedan Azure Premium och standard lagring behåller tre avbildningar av en virtuell hård disk. Användningen av en RAID-volym är enbart att konfigurera volymer som tillhandahåller tillräckligt I/O-genomflöde.

Ackumulerar ett antal Azure-VHD: er under en RAID-accumulative från en IOPS-och lagrings plats för lagring. Så om du sätter en RAID 0 över 3 x P30 Azure Premium Storage-diskar, bör det ge dig tre gånger IOPS och tre gånger lagrings data flödet för en enskild Azure Premium Storage P30-disk.

Behåll även det övergripande i/O-dataflödet för virtuella datorer i åtanke när du ändrar storlek på eller bestämmer för en virtuell dator. Det totala data flödet för VM-lagring dokumenteras i artikel [minnet optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Läge för I/O-Schemaläggaren I Linux
Linux har flera olika I/O-schemaläggnings lägen. Vanliga rekommendationer via Linux-leverantörer och SAP är att konfigurera om I/O Scheduler-läget för disk volymer från **MQ-** eller **Kyber** -läget till **Noop** (inte multiqueue) eller **ingen** för läget (multiqueue). Information finns i [SAP obs #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösningar med Premium Storage och Azure Skrivningsaccelerator för virtuella Azure-datorer i M-serien
Azure Skrivningsaccelerator är en funktion som endast är tillgänglig för virtuella datorer i Azure M-serien. Som namn tillstånd är syftet med funktionen att förbättra I/O-svars tiden för skrivningar mot Azure-Premium Storage. För SAP HANA ska Skrivningsaccelerator endast användas mot **/Hana/log** -volymen. Därför är **/Hana/data** och **/Hana/log** separata volymer med Azure Skrivningsaccelerator som bara stöder **/Hana/log** -volymen. 

> [!IMPORTANT]
> När du använder Azure Premium Storage är användningen av Azure- [Skrivningsaccelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) eller/Hana/log-volymen obligatorisk. Skrivningsaccelerator är endast tillgängligt för Premium Storage och virtuella datorer i M-serien och Mv2-serien.

Rekommendationerna för cachelagring nedan förutsätter i/O-egenskaperna för SAP HANA listan, t. ex.:

- Det finns ingen eventuell Läs arbets belastning mot HANA-datafilerna. Undantag är stora I/o efter omstart av HANA-instansen eller när data har lästs in i HANA. Ett annat fall av större Läs-I/o mot datafiler kan vara HANA-databas säkerhets kopieringar. Som ett resultat för cachelagring av utdata är det oftast inte bra eftersom i de flesta fall måste alla data fil volymer läsas fullständigt.
- Det går att skriva till datafilerna i burst utifrån HANA-lagrings punkter och HANA-krasch återställning. Skrivning av lagrings punkter är asynkront och innehåller inga användar transaktioner. Att skriva data under krasch återställning är prestanda kritiska för att få systemet att svara snabbt och enkelt. Krasch återställning bör dock vara i stället för exceptionella situationer
- Det finns inga läsningar från HANA gör om-filerna. Undantag är stora I/o när du utför säkerhets kopieringar av transaktions loggar, krasch återställning eller i start fasen för en HANA-instans.  
- Huvud belastningen mot den SAP HANA göra om-logg filen skrivs. Beroende på arbets Belastningens natur kan du ha I/o-typ så litet som 4 KB eller i andra fall i/O-storlekar på 1 MB eller mer. Skriv fördröjning mot SAP HANA gör om loggen prestanda kritisk.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt

**Rekommendation: som ett resultat av dessa observerade I/O-mönster genom SAP HANA ska cachelagring för de olika volymerna som använder Azure Premium Storage ställas in som:**

- **/Hana/data** – ingen cachelagring
- **/Hana/log** – ingen cachelagring – undantag för M-och Mv2-serien där Skrivningsaccelerator ska aktive ras utan cachelagring av läsning. 
- **/Hana/Shared** – cachelagring för läsning

### <a name="production-recommended-storage-solution"></a>Rekommenderad lagrings lösning för produktion

> [!IMPORTANT]
> SAP HANA-certifiering för Azure M-seriens virtuella datorer är exklusivt med Azure Skrivningsaccelerator för **/Hana/log** -volymen. Därför förväntas produktions scenariot SAP HANA distributioner på virtuella datorer i Azure M-serien att konfigureras med Azure Skrivningsaccelerator för **/Hana/log** -volymen.  

> [!NOTE]
> För produktions scenarier kontrollerar du om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Rekommendationerna överskrider ofta de lägsta kraven för SAP som anges tidigare i den här artikeln. De listade rekommendationerna är en kompromiss mellan storleks rekommendationerna av SAP och det maximala lagrings utrymmet som de olika VM-typerna tillhandahåller.

**Rekommendation: de rekommenderade konfigurationerna för produktions scenarier ser ut så här:**

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /hana/data | /hana/log | /hana/shared | /root-volym | /usr/sap | Hana/säkerhets kopiering |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Kontrol lera om lagrings data flödet för de olika föreslagna volymerna uppfyller den arbets belastning som du vill köra. Om arbets belastningen kräver högre volymer för **/Hana/data** och **/Hana/log**måste du öka antalet Azure Premium Storage virtuella hård diskar. Att ändra storlek på en volym med fler virtuella hård diskar än vad som anges i listan ökar IOPS-och I/O-genomflödet inom gränserna för den virtuella Azure-dator typen.

Azure-Skrivningsaccelerator fungerar bara tillsammans med [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Minst de Azure Premium Storage-diskar som utgör **/Hana/log** -volymen måste distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage virtuella hård diskar per virtuell dator som kan användas av Azure Skrivningsaccelerator. De aktuella gränserna är:

- 16 virtuella hård diskar för en M128xx-och M416xx VM
- 8 virtuella hård diskar för en M64xx-och M208xx-VM
- 4 virtuella hård diskar för en virtuell M32xx-dator

Mer detaljerad information om hur du aktiverar Azure Skrivningsaccelerator finns i artikeln [Skrivningsaccelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Skrivningsaccelerator finns i samma dokumentation.

**Rekommendation: du måste använda Skrivningsaccelerator för diskar som utgör/Hana/log-volymen**


### <a name="cost-conscious-azure-storage-configuration"></a>Kostnads medveten Azure Storage konfiguration
I följande tabell visas en konfiguration av de VM-typer som kunderna också använder för att vara värdar för SAP HANA på virtuella Azure-datorer. Det kan finnas vissa VM-typer som kanske inte uppfyller alla minimi krav för lagring för SAP HANA eller som inte stöds av SAP HANA av SAP. Men det är så långt de virtuella datorerna verkade vara bra för scenarier med icke-produktion. **/Hana/data** och **/Hana/log** kombineras till en volym. Användningen av Azure Skrivningsaccelerator kan bli en begränsning i samband med IOPS.

> [!NOTE]
> För SAP-scenarier som stöds kan du kontrol lera om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> En förändring från tidigare rekommendationer för en kostnads medveten lösning är att gå från [Azure standard HDD diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) för att bättre prestera och mer pålitliga [Azure standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Rekommendationerna överskrider ofta de lägsta kraven för SAP som anges tidigare i den här artikeln. De listade rekommendationerna är en kompromiss mellan storleks rekommendationerna av SAP och det maximala lagrings utrymmet som de olika VM-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data och/Hana/log<br /> randigt med LVM eller MDADM | /hana/shared | /root-volym | /usr/sap | Hana/säkerhets kopiering |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Diskarna som rekommenderas för de mindre VM-typerna med 3 x P20 överdimensionerade volymerna för utrymmes rekommendationer enligt [fakta bladet för SAP TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Valet som visas i tabellen gjordes dock för att ge tillräckligt med disk data flöde för SAP HANA. Om du behöver ändra **/Hana/backup** -volymen, som har storleks Visa för att bevara säkerhets kopior som representerar två gånger minnes volymen, är det kostnads fritt att justera.   
Kontrol lera om lagrings data flödet för de olika föreslagna volymerna uppfyller den arbets belastning som du vill köra. Om arbets belastningen kräver högre volymer för **/Hana/data** och **/Hana/log**måste du öka antalet Azure Premium Storage virtuella hård diskar. Att ändra storlek på en volym med fler virtuella hård diskar än vad som anges i listan ökar IOPS-och I/O-genomflödet inom gränserna för den virtuella Azure-dator typen. 

> [!NOTE]
> Konfigurationerna ovan skulle inte ha nytta av den [virtuella Azure-datorns service avtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) för virtuell dator eftersom den använder en blandning av Azure Premium Storage och Azure standard Storage. Men valet valdes för att optimera kostnaderna. Du måste välja Premium Storage för alla diskar som anges som Azure standard Storage (SXX) för att göra VM-konfigurationen kompatibel med Azure Single VM service avtal.


> [!NOTE]
> De angivna disk konfigurations rekommendationerna är till för att uppfylla minimi kraven för SAP Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier påträffades situationer där dessa rekommendationer fortfarande inte tillhandahöll tillräckliga funktioner. Det kan vara situationer där en kund behövde en snabbare inläsning av data efter en HANA-omstart eller där säkerhets kopierings konfigurationerna krävde högre bandbredd för lagringen. Andra fall som ingår **/Hana/log** där 5000 IOPS inte var tillräckligt för den speciella arbets belastningen. Så ta med rekommendationerna som en start punkt och anpassa utifrån kraven för arbets belastningen.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfiguration av Azure Ultra disk Storage för SAP HANA
Microsoft håller på att lansera en ny Azure Storage-typ som kallas [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Den stora skillnaden mellan Azure Storage och Ultra disk är att disk funktionerna inte är kopplade till disk storleken längre. Som kund kan du definiera dessa funktioner för Ultra disk:

- Storlek på en disk som sträcker sig från 4 GiB till 65 536 GiB
- IOPS sträcker sig från 100 IOPS till 160K IOPS (maximalt beror på VM-typer)
- Lagrings data flöde från 300 MB/SEK till 2 000 MB/SEK

Ultra disk ger dig möjlighet att definiera en enskild disk som uppfyller din storlek, IOPS och disk data flödes intervallet. I stället för att använda logiska volym hanterare som LVM eller MDADM ovanpå Azure Premium Storage för att skapa volymer som uppfyller kraven för IOPS och data flöde. Du kan köra en konfigurations kombination mellan Ultra disk och Premium Storage. Det innebär att du kan begränsa användningen av Ultra disk till de prestanda kritiska/Hana/data-och/Hana/log-volymerna och de andra volymerna med Azure Premium Storage

Andra fördelar med Ultra disk kan vara den bättre Läs fördröjningen i jämförelse med Premium Storage. Svars tiden för snabbare läsning kan ha fördelar när du vill minska HANA-starttiden och den efterföljande inläsningen av data i minnet. Fördelarna med Ultra disk Storage kan också vara filtpenna när HANA skriver lagrings punkter. Eftersom Premium Storage diskar för/Hana/data vanligt vis inte Skrivningsaccelerator cachelagrat, är Skriv fördröjningen till/Hana/data på Premium Storage jämfört med den Ultra disken högre. Det kan vara förväntat att skrivning av lagrings utrymme med Ultra disk presterar bättre på Ultra disk.

> [!IMPORTANT]
> Ultra disk finns ännu inte i alla Azure-regioner och har ännu inte stöd för alla VM-typer som anges nedan. Detaljerad information om hur Ultra disk är tillgängligt och vilka VM-familjer som stöds finns i artikeln [vilka disk typer som är tillgängliga i Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Rekommenderad lagrings lösning för produktion med ren Ultra disk Configuration
I den här konfigurationen behåller du/Hana/data-och/Hana/log-volymerna separat. De föreslagna värdena härleds ut från KPI: er som SAP måste certifiera VM-typer för SAP HANA-och lagrings konfigurationer enligt rekommendationerna i [fakta bladet för SAP TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Rekommendationerna överskrider ofta de lägsta kraven för SAP som anges tidigare i den här artikeln. De listade rekommendationerna är en kompromiss mellan storleks rekommendationerna av SAP och det maximala lagrings utrymmet som de olika VM-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | /Hana/data volym | /Hana/data I/O-genomflöde | /Hana/data IOPS | /Hana/log volym | /Hana/log I/O-genomflöde | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 Mbit/s | 7 500 | 512 GB | 500 Mbit/s  | 2 000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 Mbit/s | 7 500 | 256 GB | 250 Mbit/s  | 2 000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 Mbit/s | 7 500 | 256 GB | 250 Mbit/s  | 2 000 |
| M64ls | 512 GiB | 1 000 MB/s | 600 GB | 600 Mbit/s | 7 500 | 512 GB | 400 Mbit/s  | 2 500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 Mbit/s | 7 500 | 512 GB | 400 Mbit/s  | 2 500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 Mbit/s | 7 500 | 512 GB | 400 Mbit/s  | 2 500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 Mbit/s |9,000 | 512 GB | 800 Mbit/s  | 3 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 Mbit/s |9,000 | 512 GB | 800 Mbit/s  | 3 000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 Mbit/s | 9,000 | 512 GB | 400 Mbit/s  | 2 500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 Mbit/s | 9,000 | 512 GB | 400 Mbit/s  | 2 500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 Mbit/s | 9,000 | 512 GB | 800 Mbit/s  | 3 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 Mbit/s | 9,000 | 512 GB | 800 Mbit/s  | 3 000 |   

Värdena i listan är avsedda att vara en start punkt och måste utvärderas mot de verkliga kraven. Fördelen med Azure Ultra disk är att värdena för IOPS och data flöde kan anpassas utan att du behöver stänga av den virtuella datorn eller stoppa arbets belastningen som tillämpas på systemet.   

> [!NOTE]
> Lagrings ögonblicks bilder med Ultra disk Storage är hittills inte tillgängliga. Detta blockerar användningen av ögonblicks bilder av virtuella datorer med Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Kostnads medveten lagrings lösning med ren Ultra disk Configuration
I den här konfigurationen kan du/Hana/data-och/Hana/log-volymerna på samma disk. De föreslagna värdena härleds ut från KPI: er som SAP måste certifiera VM-typer för SAP HANA-och lagrings konfigurationer enligt rekommendationerna i [fakta bladet för SAP TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Rekommendationerna överskrider ofta de lägsta kraven för SAP som anges tidigare i den här artikeln. De listade rekommendationerna är en kompromiss mellan storleks rekommendationerna av SAP och det maximala lagrings utrymmet som de olika VM-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM-I/O<br /> Dataflöde | Volym för/Hana/data och/log | /Hana/data och logg I/O-dataflöde | /Hana/data och logga IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 Mbit/s | 9 500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 Mbit/s | 9 500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 Mbit/s | 9 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 Mbit/s | 10 000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 Mbit/s | 10 000 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 Mbit/s | 10 000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 Mbit/s |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 Mbit/s |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 Mbit/s | 10 000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 Mbit/s | 10 000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1, 800MBps | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 Mbit/s | 12 000 |    

Värdena i listan är avsedda att vara en start punkt och måste utvärderas mot de verkliga kraven. Fördelen med Azure Ultra disk är att värdena för IOPS och data flöde kan anpassas utan att du behöver stänga av den virtuella datorn eller stoppa arbets belastningen som tillämpas på systemet.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v 4.1-volymer på Azure NetApp Files
Azure NetApp Files tillhandahåller interna NFS-resurser som kan användas för/Hana/Shared-,/Hana/data-och/Hana/log-volymer. Att använda ANF-baserade NFS-resurser för/Hana/data-och/Hana/log-volymerna kräver att v 4.1 NFS-protokollet används. NFS-protokollet v3 stöds inte för användning av/Hana/data-och/Hana/log-volymer när du baserar resurserna på ANF. 

> [!IMPORTANT]
> Det NFS v3-protokoll som implementeras på Azure NetApp Files stöds **inte** för användning för/Hana/data och/Hana/log. Användningen av NFS 4,1 är obligatorisk för/Hana/data-och/Hana/log-volymer från en funktionell punkt i vyn. För/Hana/Shared-volymen kan NFS v3 eller NFS v 4.1-protokollet användas från en funktionell punkt i vyn.

### <a name="important-considerations"></a>Viktiga överväganden
Tänk på följande viktiga överväganden när du överväger Azure NetApp Files för SAP-NetWeaver och SAP HANA:

- Den minsta kapacitets poolen är 4 TiB.  
- Den minsta volym storleken är 100 GiB
- Azure NetApp Files och alla virtuella datorer, där Azure NetApp Files volymer ska monteras, måste finnas i samma Azure-Virtual Network eller i [peer-anslutna virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät, delegerat till Azure NetApp Files.
- Data flödet för en Azure NetApp-volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven.  
- Azure NetApp Files erbjuder [export princip](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): du kan kontrol lera tillåtna klienter, åtkomst typ (Läs&Skriv, skrivskyddad, osv.). 
- Azure NetApp Files funktionen är inte en zon medveten än. För närvarande är Azure NetApp Files funktionen inte distribuerad i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.  
- Det är viktigt att de virtuella datorerna distribueras i närheten av Azure NetApp-lagringen för låg latens. 
- Användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` på de virtuella datorerna måste matcha konfigurationen i Azure NetApp Files. 

> [!IMPORTANT]
> För SAP HANA arbets belastningar är låg latens kritiskt. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  

> [!IMPORTANT]
> Om det finns ett matchnings fel mellan användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` mellan den virtuella datorn och Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer, monterade på virtuella datorer, som `nobody` . Se till att ange rätt användar-ID för <b>sid</b>-adm och grupp-ID för `sapsys` , när [ett nytt system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ska Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Storlek för HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp-volym är en funktion i volym storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

När du designar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta krav på lagrings data flöde från SAP, som översätts till lägsta data flödes egenskaper för:

- Aktivera Läs-och skriv åtgärder på/Hana/log i 250 MB/SEK med 1 MB I/O-storlekar  
- Aktivera Läs aktivitet på minst 400 MB/SEK för/Hana/data för 16 MB och 64 MB I/O-storlekar  
- Aktivera Skriv aktivitet med minst 250 MB/SEK för/Hana/data med 16 MB och 64 MB I/O-storlekar  

[Azure NetApp Files data flödes gränser](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TIB volym kvot:
- Premium Storage nivå – 64 MiB/s  
- Ultra Storage-nivå – 128 MiB/s  

> [!IMPORTANT]
> Oberoende av kapaciteten som du distribuerar på en enskild NFS-volym förväntas data flödet till platå i intervallet 1.2 – 1,4 GB/SEK som används av en konsument i en virtuell dator. Detta måste göra med den underliggande arkitekturen i ANF-erbjudandet och relaterade Linux-sessionsgränser runt NFS. Prestanda-och data flödes numren som dokumenteras i artikeln prestandatest [resultat för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) utfördes mot en delad NFS-volym med flera virtuella klient datorer och på grund av flera sessioner. Det scenariot är annorlunda för det scenario vi mäter i SAP. Där vi mäter data flödet från en enskild virtuell dator mot en NFS-volym. finns på ANF.

För att uppfylla de lägsta data flödes kraven för SAP för data och logg, och enligt rikt linjerna för `/hana/shared` , skulle de rekommenderade storlekarna se ut så här:

| Volym | Storlek<br /> Premium Storage nivå | Storlek<br /> Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) per 4 arbetsnoder | Max (512 GB, 1xRAM) per 4 arbetsnoder | v3 eller v 4.1 |


> [!NOTE]
> De Azure NetApp Files storleks rekommendationer som anges här är riktade till att uppfylla minimi kraven för SAP-Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier, är det inte tillräckligt. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.  

Därför kan du överväga att distribuera liknande data flöde för de ANF-volymer som anges för Ultra disk Storage redan. Tänk också på storlekarna för de storlekar som anges för volymerna för de olika VM SKU: erna som utförda i de Ultra disk-tabellerna redan.

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva `unmount` volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Det gör det möjligt att uppfylla ditt program både förväntat och oförutsedda data flödes krav.

Dokumentation om hur du distribuerar en SAP HANA skalbar konfiguration med en standby-nod med hjälp av NFS v 4.1-volymer som finns i ANF publiceras i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Nästa steg
Mer information finns i:

- [SAP HANA guide för hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
