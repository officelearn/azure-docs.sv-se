---
title: SAP HANA Azure-konfigurationer för virtuell datorlagring | Microsoft-dokument
description: Lagringsrekommendationer för virtuell dator som har SAP HANA distribueras i dem.
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080316"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Lagringskonfigurationer för virtuella Azure-datorer för SAP HANA

Azure tillhandahåller olika typer av lagring som är lämpliga för virtuella Azure-datorer som kör SAP HANA. Den **SAP HANA-certifierade Azure-lagringstyper** som kan beaktas för SAP HANA-distributioner lista som: 

- Azure Premium SSD  
- [Ultradisk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Mer information om dessa disktyper finns i artikeln [Välj en disktyp](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure erbjuder två distributionsmetoder för virtuella hårddiskar på Azure Standard och Premium Storage. Om det övergripande scenariot tillåter kan du dra nytta av [Azure-hanterade](https://azure.microsoft.com/services/managed-disks/) diskdistributioner. 

En lista över lagringstyper och deras SLA:er i IOPS och lagringsdataflöde finns i [Azure-dokumentationen för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Oberoende av den azure-lagringstyp som valts måste filsystemet som används på det lagringsutrymmet stödjas av SAP för det specifika operativsystemet och DBMS. [SAP-supportanteckning #405827](https://launchpad.support.sap.com/#/notes/405827) listar de filsystem som stöds för olika operativsystem och databaser, inklusive SAP HANA. Detta gäller för alla volymer SAP HANA kan komma åt för läsning och skrivning för vilken uppgift som helst. Specifikt med NFS på Azure för SAP HANA gäller ytterligare begränsningar för NFS-versioner som anges senare i den här artikeln 


De lägsta SAP HANA-certifierade villkoren för de olika lagringstyperna är: 

- Azure Premium SSD - /hana/log måste cachelagras med Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). /hana/datavolymen kan placeras på Premium SSD utan Azure Write Accelerator eller på Ultra disk
- Azure Ultra-disken åtminstone för /hana/log-volymen. /hana/datavolymen kan placeras på antingen Premium SSD utan Azure Write Accelerator eller för att få snabbare omstartstider Ultra disk
- **NFS v4.1-volymer** ovanpå Azure NetApp-filer för /hana/log **och** /hana/data. Volymen /hana/shared kan använda NFS v3- eller NFS v4.1-protokoll. NFS v4.1-protokollet är obligatoriskt för volymer /hana/data och/hana/log.

Vissa lagringstyper kan kombineras. Det är till exempel möjligt att placera /hana/data på Premium Storage och /hana/log kan placeras på Ultra disklagring för att få den låga latens som krävs. Om du använder en NFS v4.1-volym baserat på ANF för /hana/data måste du dock använda en annan NFS v4.1-volym baserat på ANF för /hana/log. Det **går inte**att använda NFS ovanpå ANF för en av volymerna (till exempel /hana/data) och Azure Premium Storage eller Ultra-disk för den andra volymen (till exempel /hana/log).

I den lokala världen behövde man sällan bry dig om I/O-delsystemen och dess kapacitet. Anledningen var att leverantören av apparaten behövde se till att de lägsta lagringskraven är uppfyllda för SAP HANA. När du själv bygger Azure-infrastrukturen bör du vara medveten om några av dessa SAP-utfärdade krav. Några av de minsta genomströmningsegenskaper som krävs från SAP, resulterar i behovet av att:

- Aktivera läs/skriv på **/hana/log** för en 250 MB/sek med 1 MB I/O-storlekar
- Aktivera läsaktivitet på minst 400 MB/sek för **/hana/data** för 16 MB- och 64 MB I/O-storlekar
- Aktivera skrivaktivitet på minst 250 MB/sek för **/hana/data** med 16 MB- och 64 MB I/O-storlekar

Med tanke på att låg lagringsfördröjning är avgörande för DBMS-system, även som DBMS, som SAP HANA, håller data i minnet. Den kritiska linjen i lagringen är vanligtvis runt transaktionsloggens skrivningar av DBMS-systemen. Men även åtgärder som att skriva savepoints eller läsa in data i minnet efter kraschåterställning kan vara kritiska. Därför är det **obligatoriskt** att utnyttja Azure Premium Diskar för **/hana/data** och **/hana/log-volymer.** För att uppnå minsta dataflöde för **/hana/log** och **/hana/data** som önskas av SAP måste du skapa en RAID 0 med MDADM eller LVM över flera Azure Premium Storage-diskar. Och använd RAID-volymerna som **/hana/data-** och **/hana/log-volymer.** 

**Rekommendation: Som randstorlekar för RAID 0 är rekommendationen att använda:**

- 256 KB för **/hana/data**
- 32 KB för **/hana/log**

> [!IMPORTANT]
> Stripe-storleken för /hana/data ändrades från tidigare rekommendationer som krävde 64 KB eller 128 KB till 256 KB baserat på kundupplevelser med nyare Linux-versioner. Storleken på 256 KB ger något bättre prestanda

> [!NOTE]
> Du behöver inte konfigurera någon redundansnivå med RAID-volymer eftersom Azure Premium och Standard-lagring har tre avbildningar av en virtuell hårddisk. Användningen av en RAID-volym är enbart för att konfigurera volymer som ger tillräckligt I/O-dataflöde.

Ackumulera ett antal Azure VHD:er under en RAID, ackumuleras från en IOPS och lagringsdataflöde sida. Så om du sätter en RAID 0 över 3 x P30 Azure Premium Storage-diskar, bör det ge dig tre gånger IOPS och tre gånger lagringsdataflödet för en enda Azure Premium Storage P30-disk.

Också hålla den totala VM I / O-dataflöde i åtanke när dimensionering eller bestämmer för en virtuell dator. Totalt VM-lagringsdataflöde dokumenteras i artikeln [Minnesoptimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Linux I/O Scheduler-läge
Linux har flera olika I/O-schemaläggningslägen. Gemensam rekommendation via Linux-leverantörer och SAP är att konfigurera om I/O-schemaläget för diskvolymer från **cfq-läget** till **noop** -läget (icke-multiqueue) eller **ingen** för (multiqueue)-läge. Information refereras i [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösningar med Premium Storage och Azure Write Accelerator för virtuella Azure M-serien-datorer
Azure Write Accelerator är en funktion som är tillgänglig för virtuella azure M-series-datorer exklusivt. Som namnet anger är syftet med funktionerna att förbättra I/O-svarstiden för skrivningar mot Azure Premium Storage. För SAP HANA ska skrivaccelerator endast användas mot **/hana/log-volymen.** Därför är **/hana/data** och **/hana/log** separata volymer med Azure Write Accelerator som endast stöder **/hana/log-volymen.** 

> [!IMPORTANT]
> När du använder Azure Premium Storage är användningen av Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) eller /hana/log-volymen obligatorisk. Write Accelerator är endast tillgängligt för premiumlagrings- och M-seriens och Mv2-seriens virtuella datorer.

Cachelagringsrekommendationerna nedan antar I/O-egenskaperna för SAP HANA som listar som:

- Det finns knappast någon läsarbetsbelastning mot HANA-datafilerna. Undantag är stora I/Os efter omstart av HANA-instansen eller när data läses in i HANA. Ett annat fall av större läsa I / Os mot datafiler kan VARA HANA databas säkerhetskopior. Som ett resultat läsa cachelagring mestadels inte vettigt eftersom i de flesta fall, alla datafil volymer måste läsas helt.
- Skriva mot datafiler upplevs i skurar baserat av HANA savepoints och HANA krasch återhämtning. Att skriva sparpunkter är asynkront och håller inte upp några användartransaktioner. Skriva data under kraschåterställning är prestandakritiskt för att få systemet att svara snabbt igen. Återhämtning av krascher bör dock vara ganska exceptionella situationer
- Det finns knappast några läser från HANA göra om filer. Undantag är stora I/O när transaktionsloggsäkerhetskopior utförs, kraschåterställning eller i omstartsfasen av en HANA-instans.  
- Huvudsaklig belastning mot SAP HANA gör om loggfilen är skrivningar. Beroende på vilken typ av arbetsbelastning, kan du ha I / Os så liten som 4 KB eller i andra fall I / O-storlekar på 1 MB eller mer. Skrivfördröjning mot SAP HANA-gör om loggen är prestandakritisk.
- Alla skrivningar måste finnas kvar på disken på ett tillförlitligt sätt

**Rekommendation: Som ett resultat av dessa observerade I/O-mönster av SAP HANA bör cachelagringen för de olika volymerna med Azure Premium Storage anges som:**

- **/hana/data** - ingen cachelagring
- **/hana/log** - ingen cachelagring - undantag för M- och Mv2-serien där Skrivaccelerator ska aktiveras utan att läsa cachelagring. 
- **/hana/shared** - läs cachelagring

### <a name="production-recommended-storage-solution"></a>Produktionsrekomna lagringslösningar

> [!IMPORTANT]
> SAP HANA-certifiering för virtuella Azure M-serien-datorer är uteslutande med Azure Write Accelerator för **/hana/log-volymen.** Därför förväntas produktionsscenario SAP HANA-distributioner på virtuella Azure M-serien-datorer konfigureras med Azure Write Accelerator för **/hana/log-volymen.**  

> [!NOTE]
> För produktionsscenarier, kontrollera om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Rekommendationerna överskrider ofta SAP minimikrav som anges tidigare i den här artikeln. De angivna rekommendationerna är en kompromiss mellan storleksrekommendationerna från SAP och det maximala lagringsdataflödet som de olika vm-typerna tillhandahåller.

**Rekommendation: De rekommenderade konfigurationerna för produktionsscenarier ser ut:**

| VM-SKU | RAM | Max. VM I/O<br /> Dataflöde | /hana/data | /hana/log | /hana/delad | /rotvolym | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts (på andra) | 192 GiB (på andra) | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls (32000) | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls (64000000 | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB (gib) | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms (M64ms) | 1750 GiB (på andra sätt) | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB (3800 GiB) | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB (gib) | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB (gib) | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB (gib) | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB (på andra sätt) | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Kontrollera om lagringsdataflödet för de olika föreslagna volymerna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**måste du öka antalet virtuella Azure Premium Storage-hårddiskar. Om du vill ändra en volym med fler virtuella hårddiskar än i listan ökar IOPS- och I/O-dataflödet inom gränserna för typen av virtuell azure-dator.

Azure Write Accelerator fungerar bara tillsammans med [Azure-hanterade diskar](https://azure.microsoft.com/services/managed-disks/). Så åtminstone Azure Premium Storage-diskar som utgör **/hana/log-volymen** måste distribueras som hanterade diskar.

Det finns gränser för Azure Premium Storage-virtuella hårddiskar per virtuell dator som kan stödjas av Azure Write Accelerator. De nuvarande gränserna är:

- 16 virtuella hårddiskar för en M128xx och M416xx VM
- 8 virtuella hårddiskar för en M64xx och M208xx VM
- 4 virtuella hårddiskar för en M32xx VM

Mer detaljerade instruktioner om hur du aktiverar Azure Write Accelerator finns i artikeln [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Information och begränsningar för Azure Write Accelerator finns i samma dokumentation.

**Rekommendation: Du måste använda Write Accelerator för diskar som bildar /hana/log-volymen**


### <a name="cost-conscious-azure-storage-configuration"></a>Kostnadsmedveten Azure Storage-konfiguration
Följande tabell visar en konfiguration av VM-typer som kunder också använder för att vara värdar för SAP HANA på virtuella Azure-datorer. Det kan finnas vissa typer av virtuella datorer som kanske inte uppfyller alla lägsta lagringskriterier för SAP HANA eller som inte stöds officiellt med SAP HANA av SAP. Men hittills dessa virtuella datorer verkade fungera bra för icke-produktion scenarier. **/hana/data** och **/hana/log** kombineras till en volym. Därför kan användningen av Azure Write Accelerator bli en begränsning när det gäller IOPS.

> [!NOTE]
> För SAP-scenarier som stöds kontrollerar du om en viss VM-typ stöds för SAP HANA av SAP i [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> En ändring från tidigare rekommendationer för en kostnadsmedveten lösning är att gå från [Azure Standard HDD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) till bättre prestanda och mer tillförlitliga [Azure Standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Rekommendationerna överskrider ofta SAP minimikrav som anges tidigare i den här artikeln. De angivna rekommendationerna är en kompromiss mellan storleksrekommendationerna från SAP och det maximala lagringsdataflödet som de olika vm-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM I/O<br /> Dataflöde | /hana/data och /hana/log<br /> randig med LVM eller MDADM | /hana/delad | /rotvolym | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB (på andra) | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB (0) | 1 200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB (0) | 2 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts (på andra) | 192 GiB (på andra) | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls (32000) | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls (64000000 | 512 GiB | 1 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms (M64ms) | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB (på andra sätt) | 2 000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Diskarna rekommenderas för de mindre VM-typerna med 3 x P20 överdimensionerade volymerna när det gäller utrymmesrekommendationerna enligt [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Men valet som visas i tabellen gjordes för att ge tillräckligt med diskdataflöde för SAP HANA. Om du behöver ändringar i **/hana/backup-volymen,** som var dimensionerad för att hålla säkerhetskopior som representerar dubbelt så mycket minnesvolym, får du gärna justera.   
Kontrollera om lagringsdataflödet för de olika föreslagna volymerna uppfyller den arbetsbelastning som du vill köra. Om arbetsbelastningen kräver högre volymer för **/hana/data** och **/hana/log**måste du öka antalet virtuella Azure Premium Storage-hårddiskar. Om du vill ändra en volym med fler virtuella hårddiskar än i listan ökar IOPS- och I/O-dataflödet inom gränserna för typen av virtuell azure-dator. 

> [!NOTE]
> Konfigurationerna ovan skulle INTE dra nytta av [Azure virtuell dator enda vm SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) eftersom den använder en blandning av Azure Premium Storage och Azure Standard Storage. Valet valdes dock för att optimera kostnaderna. Du måste välja Premium Storage för alla diskar ovan som anges som Azure Standard Storage (Sxx) för att göra VM-konfigurationen kompatibel med Azure single VM SLA.


> [!NOTE]
> De rekommendationer för diskkonfiguration som anges är inriktade på minimikrav SOM SAP uttrycker gentemot sina infrastrukturleverantörer. I verkliga kunddistributioner och arbetsbelastningsscenarier påträffades situationer där dessa rekommendationer fortfarande inte gav tillräckliga funktioner. Det kan vara situationer där en kund krävde en snabbare omladdning av data efter en HANA-omstart eller där säkerhetskopieringskonfigurationer krävde högre bandbredd till lagringen. Andra fall inkluderade **/hana/log** där 5000 IOPS inte var tillräckliga för den specifika arbetsbelastningen. Så ta dessa rekommendationer som utgångspunkt och anpassa baserat på kraven i arbetsbelastningen.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Azure Ultra-disklagringskonfiguration för SAP HANA
Microsoft håller på att distribuera en ny Azure-lagringstyp som kallas [Azure Ultra-disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Den stora skillnaden mellan Azure-lagring som erbjuds hittills och Ultra disk är att diskfunktionerna inte är bundna till diskstorleken längre. Som kund kan du definiera dessa funktioner för Ultra disk:

- Storlek på en disk från 4 GiB till 65.536 GiB
- IOPS sträcker sig från 100 IOPS till 160K IOPS (maximalt beror på VM-typer också)
- Lagringsdataflöde från 300 MB/sek till 2 000 MB/sek

Ultra disk ger dig möjlighet att definiera en enda disk som uppfyller din storlek, IOPS och disk dataflödesområde. I stället för att använda logiska volymhanterare som LVM eller MDADM ovanpå Azure Premium Storage för att konstruera volymer som uppfyller IOPS- och lagringsdataflödeskrav. Du kan köra en konfigurationsmix mellan Ultra disk och Premium Storage. Därför kan du begränsa användningen av Ultra-disk till prestandakritiska /hana/data- och /hana/log-volymer och täcka andra volymer med Azure Premium Storage

Andra fördelar med Ultra disk kan vara bättre läs latens i jämförelse med Premium Storage. Den snabbare lässvarstiden kan ha fördelar när du vill minska HANA-starttiderna och den efterföljande belastningen av data i minnet. Fördelar med Ultra disklagring kan också kännas när HANA skriver savepoints. Eftersom Premium Storage-diskar för /hana/data vanligtvis inte är Write Accelerator cachelagrade, är skrivfördröjningen till /hana/data på Premium Storage jämfört med Ultra-disken högre. Det kan förväntas att savepoint skriva med Ultra disk fungerar bättre på Ultra disk.

> [!IMPORTANT]
> Ultra-disken finns ännu inte i alla Azure-regioner och stöder ännu inte alla vm-typer som anges nedan. Mer information om var Ultra-disk är tillgänglig och vilka vm-familjer stöds finns i artikeln [Vilka disktyper är tillgängliga i Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Produktionsrekomna lagringslösningar med ren Ultra-diskkonfiguration
I den här konfigurationen behåller du volymerna /hana/data och /hana/log separat. De föreslagna värdena härleds från de KPI:er som SAP har för att certifiera VM-typer för SAP HANA och lagringskonfigurationer enligt rekommendationen i [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Rekommendationerna överskrider ofta SAP minimikrav som anges tidigare i den här artikeln. De angivna rekommendationerna är en kompromiss mellan storleksrekommendationerna från SAP och det maximala lagringsdataflödet som de olika vm-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM I/O<br /> Dataflöde | /hana/datavolym | /hana/data I/O-dataflöde | /hana/data IOPS | /hana/log volym | /hana/log I/O-dataflöde | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB (0) | 1 200 MB/s | 600 GB | 700 MBps | 7 500 | 512 GB | 500 MBps  | 2 000 |
| M32ts (på andra) | 192 GiB (på andra) | 500 MB/s | 250 GB | 400 MBps | 7 500 | 256 GB | 250 MBps  | 2 000 |
| M32ls (32000) | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7 500 | 256 GB | 250 MBps  | 2 000 |
| M64ls (64000000 | 512 GiB | 1 000 MB/s | 600 GB | 600 MBps | 7 500 | 512 GB | 400 MBps  | 2 500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MBps | 7 500 | 512 GB | 400 MBps  | 2 500 |
| M64ms (M64ms) | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MBps | 7 500 | 512 GB | 400 MBps  | 2 500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 MBps |9,000 | 512 GB | 800 MBps  | 3 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3 000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 MBps | 9,000 | 512 GB | 400 MBps  | 2 500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 MBps | 9,000 | 512 GB | 400 MBps  | 2 500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 MBps | 9,000 | 512 GB | 800 MBps  | 3 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MBps | 9,000 | 512 GB | 800 MBps  | 3 000 |   

De angivna värdena är avsedda att vara en utgångspunkt och måste utvärderas mot de verkliga kraven. Fördelen med Azure Ultra-disken är att värdena för IOPS och dataflöde kan anpassas utan att behöva stänga av den virtuella datorn eller stoppa arbetsbelastningen som tillämpas på systemet.   

> [!NOTE]
> Hittills är lagringsögonblicksbilder med Ultra-disklagring inte tillgängliga. Detta blockerar användningen av ögonblicksbilder av virtuella datorer med Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Kostnadsmedveten lagringslösning med ren Ultra-diskkonfiguration
I den här konfigurationen kan du volymerna /hana/data och /hana/log på samma disk. De föreslagna värdena härleds från de KPI:er som SAP har för att certifiera VM-typer för SAP HANA och lagringskonfigurationer enligt rekommendationen i [SAP TDI Storage Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Rekommendationerna överskrider ofta SAP minimikrav som anges tidigare i den här artikeln. De angivna rekommendationerna är en kompromiss mellan storleksrekommendationerna från SAP och det maximala lagringsdataflödet som de olika vm-typerna tillhandahåller.

| VM-SKU | RAM | Max. VM I/O<br /> Dataflöde | Volym för /hana/data och /log | /hana/data och logga I/O-dataflöde | /hana/data och logga IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB (0) | 1 200 MB/s | 1 200 GB | 1 200 MBps | 9,500 | 
| M32ts (på andra) | 192 GiB (på andra) | 500 MB/s | 512 GB | 400 MBps | 9,500 | 
| M32ls (32000) | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9,500 | 
| M64ls (64000000 | 512 GiB | 1 000 MB/s | 1 100 GB | 900 MBps | 10 000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 MBps | 10 000 | 
| M64ms (M64ms) | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 MBps | 10 000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 MBps |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 MBps |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 MBps | 10 000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 MBps | 10 000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1 800 MBps | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 MBps | 12 000 |    

De angivna värdena är avsedda att vara en utgångspunkt och måste utvärderas mot de verkliga kraven. Fördelen med Azure Ultra-disken är att värdena för IOPS och dataflöde kan anpassas utan att behöva stänga av den virtuella datorn eller stoppa arbetsbelastningen som tillämpas på systemet.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v4.1-volymer på Azure NetApp-filer
Azure NetApp Files tillhandahåller inbyggda NFS-resurser som kan användas för volymerna /hana/shared, /hana/data och /hana/log. Om du använder ANF-baserade NFS-resurser för volymerna /hana/data och /hana/log krävs användning av v4.1 NFS-protokollet. NFS-protokollet v3 stöds inte för användning av /hana/data- och /hana/log-volymer när resurserna baseras på ANF. 

> [!IMPORTANT]
> NFS v3-protokollet som implementeras på Azure NetApp-filer stöds **inte** för att användas för /hana/data och /hana/log. Användningen av NFS 4.1 är obligatorisk för volymer för /hana/data och /hana/log ur funktionell synvinkel. För den /hana/delade volymen kan NFS v3 eller NFS v4.1-protokollet användas ur funktionell synvinkel.

### <a name="important-considerations"></a>Att tänka på
När du överväger Azure NetApp-filer för SAP Netweaver och SAP HANA bör du vara medveten om följande viktiga överväganden:

- Den minsta kapaciteten poolen är 4 TiB.  
- Den minsta volymstorleken är 100 GiB
- Azure NetApp-filer och alla virtuella datorer, där Azure NetApp-filer volymer kommer att monteras, måste vara i samma Azure Virtual Network eller i [peered virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät som delegerats till Azure NetApp-filer.
- Dataflödet för en Azure NetApp-volym är en funktion av volymkvoten och tjänstnivån, som dokumenteras på [tjänstnivå för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). När du dimensionerar HANA Azure NetApp-volymerna kontrollerar du att det resulterande dataflödet uppfyller HANA-systemkraven.  
- Azure NetApp Files erbjuder [exportprincip:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)du kan styra tillåtna klienter, åtkomsttypen (Skriv&, Skrivskyddat osv.). 
- Azure NetApp Files-funktionen är inte zonmedveten ännu. Azure NetApp Files-funktionen distribueras inte i alla tillgänglighetszoner i en Azure-region. Var medveten om de potentiella latenskonsekvenserna i vissa Azure-regioner.  
- Det är viktigt att ha de virtuella datorerna distribuerade i närheten av Azure NetApp-lagring för låg svarstid. 
- Användar-ID för <b>sid</b>adm och `sapsys` grupp-ID för på de virtuella datorerna måste matcha konfigurationen i Azure NetApp-filer. 

> [!IMPORTANT]
> För SAP HANA-arbetsbelastningar är låg latens kritisk. Arbeta med din Microsoft-representant för att se till att de virtuella datorerna och Azure NetApp Files-volymerna distribueras i närheten.  

> [!IMPORTANT]
> Om det finns en obalans mellan <b>sid</b>användar-ID för sid `sapsys` adm och grupp-ID för mellan den virtuella datorn och Azure NetApp-konfigurationen, visas `nobody`behörigheterna för filer på Azure NetApp-volymer, monterade på virtuella datorer, som . Se till att ange rätt användar-ID för <b>sid</b> `sapsys`adm och grupp-ID för , när [du går ombord på ett nytt system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) till Azure NetApp-filer.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionering för HANA-databas på Azure NetApp-filer

Dataflödet för en Azure NetApp-volym är en funktion av volymstorlek och tjänstnivå, som dokumenteras på [tjänstnivå för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om några minimikrav för lagringsdataflöde av SAP, vilket leder till minsta dataflödesegenskaper för:

- Aktivera läs/skriv på /hana/log för en 250 MB/sek med 1 MB I/O-storlekar  
- Aktivera läsaktivitet på minst 400 MB/sek för /hana/data för 16 MB- och 64 MB I/O-storlekar  
- Aktivera skrivaktivitet på minst 250 MB/sek för /hana/data med 16 MB- och 64 MB I/O-storlekar  

[Azure NetApp-dataflödesgränserna](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB volymkvot är:
- Premium lagringsnivå - 64 MiB/s  
- Ultra LagringSnivå – 128 MiB/s  

> [!IMPORTANT]
> Oberoende av den kapacitet som du distribuerar på en enda NFS-volym förväntas dataflödet att plana ut i intervallet 1,2-1,4 GB/sek bandbredd som utnyttjas av en konsument på en virtuell dator. Detta har att göra med den underliggande arkitekturen i ANF-erbjudandet och relaterade Linux-sessionsgränser runt NFS. Prestanda- och dataflödesnumren som dokumenteras i artikeln [Prestandariktmärkenstestresultat för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) utfördes mot en delad NFS-volym med flera virtuella klient-datorer och som ett resultat med flera sessioner. Det scenariot skiljer sig från det scenario som vi mäter i SAP. Där vi mäter dataflöde från en enda virtuell dator mot en NFS-volym. värd på ANF.

För att uppfylla sap minimikrav för dataflöde för data `/hana/shared`och logg, och enligt riktlinjerna för , de rekommenderade storlekarna skulle se ut:

| Volym | Storlek<br /> Premium lagringsnivå | Storlek<br /> Ultra lagringsnivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/delad | Max(512 GB, 1xRAM) per 4 arbetsnoder | Max(512 GB, 1xRAM) per 4 arbetsnoder | v3 eller v4.1 |


> [!NOTE]
> Azure NetApp-filer storleksrekommendationer som anges här är inriktade på att uppfylla de minimikrav SAP uttrycker mot sina infrastrukturleverantörer. I verkliga kunddistributioner och arbetsbelastningsscenarier kanske det inte räcker. Använd dessa rekommendationer som utgångspunkt och anpassa, baserat på kraven för din specifika arbetsbelastning.  

Därför kan du överväga att distribuera liknande dataflöde för ANF-volymer som anges för Ultra disklagring redan. Tänk också på storlekarna för de storlekar som anges för volymerna för de olika virtuella SKU:erna som redan gjorts i Ultra-disktabellerna.

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files-volymer dynamiskt, utan att `unmount` volymerna behöver göras, stoppa de virtuella datorerna eller stoppa SAP HANA. Det ger flexibilitet för att uppfylla dina program både förväntade och oförutsedda krav på dataflöde.

Dokumentation om hur du distribuerar en SAP HANA-utskalningskonfiguration med standby-nod med NFS v4.1-volymer som finns i ANF publiceras i [SAP HANA-utskalning med väntenod på Virtuella Azure-datorer med Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Nästa steg
Mer information finns i:

- [SAP HANA Guide för hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
