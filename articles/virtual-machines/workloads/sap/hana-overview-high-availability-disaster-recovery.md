---
title: Hög tillgänglighet och haveriberedskap för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Upprätta hög tillgänglighet och planera för haveriberedskap av SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0585c1251ba18e1390f3eee28a989edee6eb8591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616933"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA Stora instanser med hög tillgänglighet och haveriberedskap på Azure 

>[!IMPORTANT]
>Den här dokumentationen ersätter inte SAP HANA-administrationsdokumentationen eller SAP Notes. Det förväntas att läsaren har en gedigen förståelse för och expertis inom SAP HANA administration och drift, särskilt med ämnena säkerhetskopiering, återställning, hög tillgänglighet och katastrofåterställning.

Det är viktigt att du tränar steg och processer som tas i din miljö och med dina HANA-versioner och utgåvor. Vissa processer som beskrivs i den här dokumentationen förenklas för en bättre allmän förståelse och är inte avsedda att användas som detaljerade steg för slutliga drifthandböcker. Om du vill skapa åtgärdshandböcker för dina konfigurationer måste du testa och träna dina processer och dokumentera de processer som är relaterade till dina specifika konfigurationer. 


Dr (High Availability and disaster recovery) är viktiga aspekter av att köra din verksamhetskritiska SAP HANA på Azure-servern (Stora instanser). Det är viktigt att arbeta med SAP, systemintegratören eller Microsoft för att arkitekten ska utformas och implementera rätt strategier för hög tillgänglighet och katastrofåterställning. Det är också viktigt att tänka på återställningspunktsmålet (RPO) och återställningstidens mål, som är specifika för din miljö.

Microsoft stöder vissa SAP HANA-funktioner med hög tillgänglighet med STORA HANA-instanser. Dessa funktioner är:

- **Lagringsreplikering:** Lagringssystemets förmåga att replikera alla data till en annan HANA-stämpel för stor instans i en annan Azure-region. SAP HANA fungerar oberoende av denna metod. Den här funktionen är standardmekanismen för haveriberedskap som erbjuds för STORA HANA-instanser.
- **HANA-systemreplikering**: [Replikering av alla data i SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) till ett separat SAP HANA-system. Återställningstidens mål minimeras genom datareplikering med jämna mellanrum. SAP HANA stöder asynkrona, synkrona i minnet och synkrona lägen. Synkron läge används endast för SAP HANA-system som finns inom samma datacenter eller mindre än 100 km från varandra. Med den aktuella utformningen av HANA Large Instance-stämplar kan HANA-systemreplikering endast användas för hög tillgänglighet inom en region. HANA-systemreplikering kräver en omvänd proxy- eller routningskomponent från tredje part för konfigurationer för haveriberedskap till en annan Azure-region. 
- **Värd för automatisk redundans**: En lokal felåterställningslösning för SAP HANA som är ett alternativ till HANA-systemreplikering. Om huvudnoden blir otillgänglig konfigurerar du en eller flera SAP HANA-klientdelar i utskalningsläge och SAP HANA växlar automatiskt över till en standby-nod.

SAP HANA på Azure (Stora instanser) erbjuds i två Azure-regioner i fyra geopolitiska områden (USA, Australien, Europa och Japan). Två regioner inom ett geopolitiskt område som är värd för HANA Large Instance-stämplar är anslutna till separata dedikerade nätverkskretsar. Dessa används för att replikera lagringsögonblicksbilder för att tillhandahålla metoder för haveriberedskap. Replikeringen har inte upprättats som standard men har ställts in för kunder som beställer funktionen för haveriberedskap. Lagringsreplikering är beroende av användningen av ögonblicksbilder av lagring för STORA HANA-instanser. Det går inte att välja en Azure-region som en DR-region som finns i ett annat geopolitiskt område. 

I följande tabell visas metoder och kombinationer för hög tillgänglighet och haveriberedskap som stöds:

| Scenario som stöds i STORA HANA-instanser | Alternativ för hög tillgänglighet | Alternativ för haveriberedskap | Kommentarer |
| --- | --- | --- | --- |
| Enkel nod | Inte tillgängligt. | Dedikerad DR-inställning.<br /> Multipurpose DR setup. | |
| Automatisk redundans för värd: Utskalning (med eller utan vänteläge)<br /> inklusive 1+1 | Möjligt med standby tar den aktiva rollen.<br /> HANA styr rollväxeln. | Dedikerad DR-inställning.<br /> Multipurpose DR setup.<br /> DR-synkronisering med hjälp av lagringsreplikering. | HANA-volymuppsättningar är kopplade till alla noder.<br /> DR-platsen måste ha samma antal noder. |
| HANA-systemreplikering | Möjligt med primär eller sekundär inställning.<br /> Sekundära flyttar till primär roll i ett redundansfall.<br /> HANA-systemreplikering och OS-kontroll redundans. | Dedikerad DR-inställning.<br /> Multipurpose DR setup.<br /> DR-synkronisering med hjälp av lagringsreplikering.<br /> DR med hjälp av HANA-systemreplikering är ännu inte möjligt utan komponenter från tredje part. | Separata uppsättning diskvolymer är kopplade till varje nod.<br /> Endast diskvolymer för sekundär replik på produktionsplatsen replikeras till DR-platsen.<br /> En uppsättning volymer krävs på DR-platsen. | 

En dedikerad DR-inställning är där HANA Large Instance-enheten på DR-platsen inte används för att köra någon annan arbetsbelastning eller icke-produktionssystem. Enheten är passiv och distribueras endast om en katastrof redundans körs. Den här inställningen är dock inte ett val för många kunder.

Se [scenarier som stöds av HLI](hana-supported-scenario.md) för att lära dig lagringslayout och Ethernet-information för din arkitektur.

> [!NOTE]
> [SAP HANA MCOD-distributioner](https://launchpad.support.sap.com/#/notes/1681092) (flera HANA-instanser på en enhet) som överlagringsscenarier fungerar med HA- och DR-metoderna i tabellen. Ett undantag är användningen av HANA System Replication med ett automatiskt redundanskluster baserat på Pacemaker. Ett sådant fall stöder endast en HANA-instans per enhet. För [SAP HANA MDC-distributioner](https://launchpad.support.sap.com/#/notes/2096000) fungerar endast icke-lagringsbaserade HA- och DR-metoder om mer än en klient distribueras. Med en klient distribuerad är alla metoder som anges giltiga.  

En dr-konfiguration med flera data är där HANA Large Instance-enheten på DR-platsen kör en icke-produktionsarbetsbelastning. Vid katastrof stänger du av icke-produktionssystemet, monterar de lagringsdr replikerade (ytterligare) volymuppsättningarna och startar sedan produktions-HANA-instansen. De flesta kunder som använder funktionen för haveriberedskap för HANA-instans använder den här konfigurationen. 


Du hittar mer information om SAP HANA hög tillgänglighet i följande SAP artiklar: 

- [SAP HANA Whitepaper med hög tillgänglighet](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [ADMINISTRATIONSGUIDE FÖR SAP HANA](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy Video på SAP HANA System Replication](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Support Note #1999880 – vanliga frågor och svar om SAP HANA System Replication](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP-supportanteckning #2165547 – SAP HANA Säkerhetskopierar och återställer inom SAP HANA Systemreplikeringsmiljö](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP-supportanteckning #1984882 – använda SAP HANA SystemReplikering för maskinvaruutbyte med minsta/noll driftstopp](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Nätverksöverväganden för haveriberedskap med STORA HANA-instanser

För att dra nytta av funktionen för haveriberedskap i STORA HANA-instanser måste du utforma nätverksanslutningen till de två Azure-regionerna. Du behöver en Azure ExpressRoute-kretsanslutning från lokala i din huvudsakliga Azure-region och en annan kretsanslutning från lokalt till din katastrofåterställningsregion. Den här åtgärden täcker en situation där det finns ett problem i en Azure-region, inklusive en MSEE-plats (Microsoft Enterprise Edge Router).

Som en andra åtgärd kan du ansluta alla virtuella Azure-nätverk som ansluter till SAP HANA på Azure (stora instanser) i en region till en ExpressRoute-krets som ansluter STORA HANA-instanser i den andra regionen. Med den här *korsanslutningen*kan tjänster som körs på ett virtuellt Azure-nätverk i region 1 ansluta till STORA HANA-instansenheter i region 2 och tvärtom. Den här åtgärden åtgärdar ett fall där endast en av DE MSEE-platser som ansluter till din lokala plats med Azure kopplas från.

Följande bild illustrerar en flexibel konfiguration för katastrofåterställningsfall:

![Optimal konfiguration för haveriberedskap](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Andra krav med HANA Large Instances lagringsreplikering för haveriberedskap

Förutom de föregående kraven för en inställning för haveriberedskap med STORA HANA-instanser måste du:

- Beställ SAP HANA på Azure (Stora instanser) SKU:er av samma storlek som dina produktionsskrona och distribuera dem i regionen för haveriberedskap. I de aktuella kunddistributionerna används dessa instanser för att köra HANA-instanser som inte är produktionsbaserade. Dessa konfigurationer kallas *dr-inställningar*med flera ändamål .   
- Beställ ytterligare lagringsutrymme på DR-platsen för var och en av dina SAP HANA på Azure (Stora instanser) SKU:er som du vill återställa på platsen för haveriberedskap. Genom att köpa ytterligare lagringsutrymme kan du allokera lagringsvolymerna. Du kan allokera de volymer som är målet för lagringsreplikeringen från din azure-region för produktion till Azure-regionen för haveriberedskap.
- Om du har HSR-inställningar på primär och du ställer in lagringsbaserad replikering till DR-platsen, måste du köpa ytterligare lagringsutrymme på DR-platsen så att både primära och sekundära noder data replikeras till DR-platsen.

  **Nästa steg**
- Se [Säkerhetskopiering och återställning](hana-backup-restore.md).













