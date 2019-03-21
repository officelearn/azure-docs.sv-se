---
title: Hög tillgänglighet och katastrofåterställning återställning av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Upprätta hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4d60f6752bf369e875c350823f76854408fcb806
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000602"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA stora instanser hög tillgänglighet och katastrofåterställning recovery på Azure 

>[!IMPORTANT]
>Den här dokumentationen är ingen ersättning av dokumentationen för administration av SAP HANA eller SAP Notes. Det förväntas att läsaren har en djupare förståelse av och expertis i SAP HANA-administration och åtgärder, särskilt med ämnen för säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning.

Det är viktigt att du arbeta med steg och processer som tas i miljön och med dina HANA-versioner och versioner. Vissa processer som beskrivs i den här dokumentationen är förenklad för en bättre förståelse och är inte avsedda att användas som detaljerade anvisningar för slutlig åtgärden handböcker. Om du vill skapa åtgärden handböcker för dina konfigurationer måste du testa och arbeta med dina processer och dokumentera de processer som rör dina specifika konfigurationer. 


Hög tillgänglighet och katastrofåterställning (DR) är viktiga aspekter av körning av verksamhetskritiska SAP HANA på Azure (stora instanser)-servern. Det är viktigt att arbeta med SAP, systemintegratör eller Microsoft att korrekt konstruera och implementera rätt hög tillgänglighet och strategier för haveriberedskap. Det är också viktigt att tänka på mål för återställningspunkt (RPO) och återställningstid, som är specifika för din miljö.

Microsoft stöder vissa funktioner för hög tillgänglighet av SAP HANA med stora HANA-instanser. Dessa funktioner är:

- **Lagringsreplikering**: Storage-system kan du replikera alla data till en annan stora HANA-instansen stämpel i en annan Azure-region. SAP HANA fungerar oberoende av den här metoden. Den här funktionen är disaster recovery standardmekanismen erbjuds för stora HANA-instanser.
- **HANA-systemreplikering**: Den [replikering av alla data i SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) till ett separat SAP HANA-system. Återställningstid minimeras via replikering av data med jämna mellanrum. SAP HANA stöder asynkrona, synkron i minnet och synkrona lägen. Synkront läge används endast för SAP HANA-system som ligger inom samma datacenter eller mindre än 100 km från varandra. Med den aktuella designen av stora HANA-instansen stämplar kan HANA-systemreplikering användas för hög tillgänglighet inom en region. HANA-systemreplikering kräver en tredje parts omvänd proxy eller Routning komponent för konfigurationer med katastrofåterställning i en annan Azure-region. 
- **Värdbaserad automatisk redundans**: En lokal fault-lösning för SAP HANA som är ett alternativ till HANA-systemreplikering. Om huvudnoden blir otillgänglig kan du konfigurera en eller flera vänteläge SAP HANA-noder i skalbara läge och SAP HANA växlar automatiskt över till en standby-nod.

SAP HANA på Azure (stora instanser) erbjuds i två Azure-regioner i fyra geopolitiska områden (USA, Australien, Europa och Japan). Två regioner inom en geopolitisk område som är värdar för stora HANA-instansen stämplar är anslutna till separat dedikerat nätverk kretsar. De används för replikering av ögonblicksbilder av lagring för att tillhandahålla disaster recovery-metoder. Replikeringen upprättas inte som standard men har angetts för kunder som order funktioner för haveriberedskap. Storage-replikering är beroende av användningen av storage-ögonblicksbilder för stora HANA-instanser. Det går inte att välja en Azure-region som en DR-region som ligger i ett annat område i geopolitisk. 

I följande tabell visar för närvarande stöds hög tillgänglighet och katastrofåterställning återställningsmetoder och kombinationer:

| Scenariot som stöd i stora HANA-instanser | Alternativ för hög tillgänglighet | Haveriberedskapsalternativ | Kommentarer |
| --- | --- | --- | --- |
| Enkel nod | Inte tillgängligt. | Dedikerat DR-installationen.<br /> Installationen av Multipurpose DR. | |
| Värden automatisk redundans: Skala ut (med eller utan standby)<br /> inklusive 1 + 1 | Möjligt med vänteläge tar rollen active.<br /> HANA styr växeln rollen. | Dedikerat DR-installationen.<br /> Installationen av Multipurpose DR.<br /> Synkronisering av DR med hjälp av storage-replikering. | HANA volymuppsättningar är kopplade till alla noder.<br /> DR-plats måste ha samma antal noder. |
| HANA-systemreplikering | Möjligt med primär eller sekundär installationen.<br /> Sekundär flyttar till primär roll i fall redundans.<br /> HANA-systemreplikering och OS styra redundans. | Dedikerat DR-installationen.<br /> Installationen av Multipurpose DR.<br /> Synkronisering av DR med hjälp av storage-replikering.<br /> DR med hjälp av HANA-systemreplikering är ännu inte möjligt utan komponenter från tredje part. | Separat uppsättning volymer som är kopplade till varje nod.<br /> Endast volymerna på sekundär replik i produktionsplatsen replikeras till DR-plats.<br /> En uppsättning volymer krävs på DR-plats. | 

En dedikerad DR-konfiguration är där stora HANA-instansen-enhet i DR-plats inte används för att köra andra arbetsbelastning eller icke-produktionssystem. Enheten är passivt och distribueras endast om en katastrof redundansväxling körs. Den här konfigurationen är dock inte ett önskade alternativ för många kunder.

Se [HLI stöds scenarier](hana-supported-scenario.md) mer lagring layout och Ethernet-information för din arkitektur.

> [!NOTE]
> [SAP HANA MCOD distributioner](https://launchpad.support.sap.com/#/notes/1681092) (flera HANA-instanser på en enhet) som överlägg scenarier arbete med HA och DR till metoder som anges i tabellen. Ett undantag är användningen av HANA System Replication med automatisk redundans-kluster utifrån Pacemaker. Dessa fall stöder bara en HANA-instans per enhet. För [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) distributioner, endast icke-storage-baserade HA och DR metoderna om fler än en klient har distribuerats. Med en klient har distribuerats, är alla metoder som visas giltiga.  

En multipurpose DR-konfiguration är där en produktionsarbetsbelastning körs i stora HANA-instansen enheten på DR-plats. Montera lagring replikeras (ytterligare) volymuppsättningar händelse av katastrof, stänga av systemet inte är i produktion och starta sedan produktion HANA-instans. De flesta kunder som använder stora HANA-instansen disaster recovery-funktionen använder den här konfigurationen. 


Du hittar mer information om hög tillgänglighet för SAP HANA i följande SAP-artiklar: 

- [SAP HANA hög tillgänglighet – White Paper](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Administrationsguide för SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy-Video på SAP HANA-Systemreplikering](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Support Obs! #1999880 – vanliga frågor och svar på SAP HANA-Systemreplikering](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Support Obs! #2165547 – SAP HANA tillbaka upp och återställa i SAP HANA System Replication miljö](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Support Obs! #1984882 – med hjälp av SAP HANA-Systemreplikering för maskinvara Exchange med minst/nedtid](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Nätverksöverväganden för haveriberedskap med stora HANA-instanser

Om du vill dra nytta av disaster recovery-funktionen för HANA stora instanser, måste du utforma nätverksanslutning till två Azure-regioner. Du behöver en Azure ExpressRoute-krets-anslutning från lokala i dina viktigaste Azure-region och en annan krets anslutning från en lokal plats till ditt disaster recovery-region. Det här måttet beskriver en situation där det finns ett problem i en Azure-region, inklusive en plats för Microsoft Enterprise Edge Router (MSEE).

Du kan ansluta alla Azure-nätverk som ansluter till SAP HANA på Azure (stora instanser) i en region till en ExpressRoute-krets som ansluter stora HANA-instanser i den andra regionen som en andra åtgärd. Med den här *mellan ansluta*, tjänster som körs på en Azure-nätverk i regionen 1 kan ansluta till enheter för stora HANA-instans i regionen 2 och tvärtom. Det här måttet löser ett fall där endast en av platserna som msee: N som ansluter till din lokala plats med Azure kopplas från.

Följande bild illustrerar en flexibel konfiguration för disaster recovery fall:

![Optimal konfiguration för katastrofåterställning](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Andra krav med stora HANA-instanser storage-replikering för haveriberedskap

Utöver föregående kraven för en disaster recovery-installation med stora HANA-instanser måste du:

- Ordna SAP HANA på Azure (stora instanser) SKU: er av samma storlek som din SKU: er i produktionsmiljön och distribuera dem i regionen disaster recovery. I de aktuella kunddistributioner används instanserna för att köra icke-produktion HANA-instanser. De här konfigurationerna kallas *multipurpose DR inställningar*.   
- Ordna ytterligare lagringsutrymme på webbplatsen DR för var och en av SAP HANA på Azure (stora instanser) SKU: er som du vill återställa i katastrofåterställningsplatsen. Köpa mer lagringsutrymme kan du allokera lagringsvolymer. Du kan allokera volymer som är målet för storage-replikering från din Azure-region i produktionsmiljön i den Azure-region för haveriberedskap.
- I fall där du har konfigurerat för HSR på primära och du konfigurerar lagring baserat replikering till DR-plats, måste du köpa extra lagringsutrymme i DR-plats så både primära och sekundära noder data replikeras till DR-plats.

  **Nästa steg**
- Se [säkerhetskopiering och återställning](hana-backup-restore.md).













