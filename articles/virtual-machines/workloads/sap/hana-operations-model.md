---
title: Driftmodell av SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Driftmodell av SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36a648e2d46cce96a8ff663f45ccf45326898a84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417014"
---
# <a name="operations-model-and-responsibilities"></a>Driftmodell och ansvarsområden

Den tillhandahållna med SAP HANA på Azure (stora instanser) är i linje med Azure IaaS-tjänster. Du får en instans av en stora HANA-instans med ett installerat operativsystem som är optimerad för SAP HANA. Som med Azure IaaS-VM, de flesta uppgifter härda Operativsystemet och installera ytterligare programvara, installera HANA, OS och HANA, uppdatera operativsystem och HANA är ditt ansvar. Microsoft göra inte uppdateringar av OS- eller HANA uppdateringar på du.

![Ansvaret för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

I diagrammet visas är SAP HANA på Azure (stora instanser) en flera innehavare som erbjuder IaaS. För det mesta har divisionen av ansvar nått gränsen för OS-infrastruktur. Microsoft ansvarar för alla aspekter av tjänsten under raden i operativsystemet. Du är ansvarig för alla aspekter av tjänsten ovanför raden. Operativsystemet är ditt ansvar. Du kan fortsätta att använda den mest aktuella lokala metoder du kan använda för efterlevnad, säkerhet, programhantering, dag och OS-hantering. System som visas som om de finns i nätverket när det kommer alla.

Den här tjänsten är optimerad för SAP HANA, så det finns områden där du behöver arbeta med Microsoft för att använda funktionerna för underliggande infrastruktur för bästa resultat.

I följande lista finns mer information på varje lager och dina ansvarsområden:

**Nätverk**: Alla interna nätverk för stor instans stämpeln köra SAP HANA. Ditt ansvar ger åtkomst till lagring, anslutning mellan instanser (för skalbarhet och andra funktioner), anslutning till liggande och anslutningar till Azure där SAP-programnivån finns i virtuella datorer. Den innehåller också WAN-anslutning mellan Azure-Datacenter för disaster recovery-replikering för syften. Alla nätverk partitioneras av klienten och har tjänstkvalitet tillämpas.

**Storage**: Den virtualiserade partitionerade lagring för alla volymer som krävs av SAP HANA-servrar, samt för ögonblicksbilder. 

**Servrar**: Särskilda fysiska servrar att köra SAP HANA-databaser som är tilldelad till klienter. Servrar av typen jag klassen SKU: er är maskinvara som sådant. Med den här typen av servrar, serverkonfigurationen samlas in och underhålls i profiler som kan flyttas från en fysisk maskinvara till en annan fysisk maskinvara. Sådana (manuellt) flytta för en profil av åtgärder kan jämföras lite med Azure tjänståterställning. Servrarna i SKU: er för Type II-klass erbjuder inte en sådan funktion.

**SDDC**: Programvaran för hantering som används för att hantera data datacenter som programdefinierade entiteter. Det kan Microsoft poolresurser för skalbarhet, tillgänglighet och prestanda.

**O/S**: Operativsystemet som du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. OS-avbildningar som du levereras med tillhandahölls av enskilda Linux-Försäljare till Microsoft för att köra SAP HANA. Du måste ha en prenumeration med Linux-försäljare för den specifika bilden för SAP HANA-optimerade. Du ansvarar för att registrera avbildningarna med OS-leverantören. 

Från det datum då jourtjänstöverlämnande av Microsoft är du ansvarig för eventuella ytterligare korrigeringar av Linux-operativsystem. Denna uppdatering innehåller ytterligare paket som kan krävas för en lyckad SAP HANA-installation och som inte finns med den specifika Linux-leverantör i sina SAP HANA optimerade OS-avbildningar. (Mer information finns i SAP: s HANA installationsdokumentationen och SAP Notes.) 

Du ansvarar för underhåll av operativsystem på grund av fel på utrustningen eller dess drivrutiner i förhållande till den specifika servermaskinvaran och optimering av Operativsystemet. Du är också ansvarig för säkerhets- eller funktionella uppdatering av Operativsystemet. 

Ditt ansvar omfattar också övervakning och kapacitetsplanering för:

- CPU-resursförbrukning.
- Minnesanvändningen.
- Diskvolymer relaterar till ledigt utrymme, IOPS och svarstider.
- Volymen nätverkstrafik mellan stora HANA-instansen och SAP-programnivån.

Den underliggande infrastrukturen med stora HANA-instansen innehåller funktioner för säkerhetskopiering och återställning av systemvolymen. Med den här funktionen är också ansvarig.

**Middleware**: SAP HANA-instansen, främst. Är du ansvarig för administration, åtgärder och övervakning. Du kan använda de angivna funktionerna för att använda ögonblicksbilder av lagring för säkerhetskopiering, återställning och disaster recovery-syften. Dessa funktioner tillhandahålls av infrastrukturen. Dina ansvarsområden även innehålla designa hög tillgänglighet och haveriberedskap med dessa funktioner kan utnyttja dem och övervaka att avgöra om ögonblicksbilder av lagring har körts.

**Data**: Dina data hanteras av SAP HANA och andra data, till exempel säkerhetskopior delar filer i volymer eller fil. Dina ansvarsområden omfattar övervaka ledigt diskutrymme och hantera innehåll på volymerna. Du är också ansvarig för att övervaka säkerhetskopior av volymer på diskar och ögonblicksbilder av lagring har körts. Lyckad körning av datareplikering till katastrofåterställningsplatser ansvarar för Microsoft.

**Program:** Programinstanser SAP eller när det gäller icke-SAP-program, programlagret för programmen. Dina ansvarsområden omfattar distribution, administration, åtgärder och övervakning av dessa program. Du är ansvarig för kapacitetsplanering för CPU-resursförbrukning, minnesförbrukning, Azure Storage-förbrukning och förbrukningen av nätverksbandbredd i virtuella nätverk. Du är också ansvarig för kapacitetsplanering för resursförbrukning från virtuella nätverk till SAP HANA på Azure (stora instanser).

**WAN-nätverk**: Anslutningar som du har upprättat från lokalt till Azure-distributioner för arbetsbelastningar. Alla kunder med stora HANA-instansen använder Azure ExpressRoute för anslutning. Den här anslutningen är inte en del av SAP HANA på Azure (stora instanser)-lösningen. Du är ansvarig för installationen av den här anslutningen.

**Arkivera**: Du kanske föredrar att arkivera kopior av data med hjälp av dina egna metoder i storage-konton. Arkivering kräver hantering, efterlevnad, kostnader och åtgärder. Du ansvarar för att generera Arkiv kopior och säkerhetskopieringar på Azure och lagra dem på ett sätt som är kompatibla.

Se den [SLA för SAP HANA på Azure (stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Nästa steg**
- Se [SAP HANA (stora instanser)-arkitektur på Azure](hana-architecture.md)
