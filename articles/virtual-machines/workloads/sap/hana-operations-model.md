---
title: Verksamhetsmodell för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Verksamhetsmodell för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616980"
---
# <a name="operations-model-and-responsibilities"></a>Driftmodell och ansvarsområden

Tjänsten som medföljer SAP HANA på Azure (stora instanser) är anpassad till Azure IaaS-tjänster. Du får en instans av en HANA Large Instance med ett installerat operativsystem som är optimerat för SAP HANA. Precis som med virtuella Azure IaaS-datorer är de flesta av uppgifterna att härda operativsystemet, installera ytterligare programvara, installera HANA, driva OS och HANA och uppdatera operativsystemet och HANA ditt ansvar. Microsoft tvingar inte os-uppdateringar eller HANA-uppdateringar på dig.

![ANSVAR för SAP HANA på Azure (stora instanser)](./media/hana-overview-architecture/image2-responsibilities.png)

Som visas i diagrammet är SAP HANA på Azure (stora instanser) ett IaaS-erbjudande med flera innehavare. För det mesta är ansvarsfördelningen vid OS-infrastrukturgränsen. Microsoft ansvarar för alla aspekter av tjänsten under operativsystemets linje. Du är ansvarig för alla aspekter av tjänsten ovanför linjen. Operativsystemet är ditt ansvar. Du kan fortsätta att använda de flesta aktuella lokala metoder som du kan använda för efterlevnad, säkerhet, programhantering, bas och OS-hantering. Systemen visas som om de finns i nätverket i alla avseenden.

Den här tjänsten är optimerad för SAP HANA, så det finns områden där du måste arbeta med Microsoft för att använda de underliggande infrastrukturfunktionerna för bästa resultat.

Följande lista innehåller mer information om vart och ett av skikten och dina ansvarsområden:

**Nätverk:** Alla interna nätverk för stämpeln Stor instans som kör SAP HANA. Ditt ansvar omfattar åtkomst till lagring, anslutning mellan instanser (för utskalning och andra funktioner), anslutning till landskapet och anslutning till Azure där SAP-programlagret finns i virtuella datorer. Den innehåller också WAN-anslutning mellan Azure Data Centers för haveriberedskap. Alla nätverk partitioneras av klienten och har servicekvalitet tillämpas.

**Lagring**: Den virtualiserade partitionerade lagringen för alla volymer som behövs av SAP HANA-servrarna, samt för ögonblicksbilder. 

**Servrar**: Dedikerade fysiska servrar för att köra SAP HANA DBs som tilldelats klienter. Servrarna i klass typ I-klassen av SKU:er är hårdvarstrat. Med dessa typer av servrar samlas serverkonfigurationen in och underhålls i profiler, som kan flyttas från en fysisk maskinvara till en annan fysisk maskinvara. En sådan (manuell) flyttning av en profil av åtgärder kan jämföras lite med Azure-tjänstläkning. Servrarna i klass-SKU:erna typ II erbjuder inte en sådan funktion.

**SDDC**: Den programvara för hantering som används för att hantera datacenter som programvarudefinierade enheter. Det gör att Microsoft kan samla resurser för skala, tillgänglighet och prestandaskäl.

**O /S:** Det operativsystem du väljer (SUSE Linux eller Red Hat Linux) som körs på servrarna. De OS-avbildningar som du medföljer tillhandahölls av den enskilda Linux-leverantören till Microsoft för att köra SAP HANA. Du måste ha en prenumeration hos Linux-leverantören för den specifika SAP HANA-optimerade avbildningen. Du är ansvarig för att registrera avbildningarna hos OS-leverantören. 

Från den punkt av överlämnande av Microsoft, är du ansvarig för ytterligare korrigering av Linux-operativsystemet. Den här korrigeringen innehåller ytterligare paket som kan vara nödvändiga för en lyckad SAP HANA-installation och som inte ingick av den specifika Linux-leverantören i deras SAP HANA-optimerade OS-avbildningar. (Mer information finns i SAP:s HANA-installationsdokumentation och SAP Notes.) 

Du är ansvarig för OS-korrigering på grund av fel eller optimering av operativsystemet och dess drivrutiner i förhållande till den specifika servermaskinvaran. Du är också ansvarig för säkerhet eller funktionell korrigering av operativsystemet. 

Ditt ansvar omfattar även övervakning och kapacitetsplanering av:

- Cpu-resursförbrukning.
- Minnesförbrukning.
- Diskvolymer relaterade till ledigt utrymme, IOPS och svarstid.
- Nätverksvolymtrafik mellan HANA Large Instance och SAP-programlagret.

Den underliggande infrastrukturen för HANA Large Instance tillhandahåller funktioner för säkerhetskopiering och återställning av OS-volymen. Att använda den här funktionen är också ditt ansvar.

**Middleware**: SAP HANA-instansen, främst. Administration, drift och övervakning är ditt ansvar. Du kan använda de medföljande funktionerna för att använda ögonblicksbilder av lagring för säkerhetskopiering och återställning och katastrofåterställning. Dessa funktioner tillhandahålls av infrastrukturen. Ditt ansvar omfattar också att utforma hög tillgänglighet eller haveriberedskap med dessa funktioner, utnyttja dem och övervaka för att avgöra om lagringsögonblicksbilder har körts.

**Data**: Dina data som hanteras av SAP HANA och andra data, till exempel säkerhetskopior, filer som finns på volymer eller filresurser. Ditt ansvar omfattar övervakning av diskfritt utrymme och hantering av innehållet på volymerna. Du är också ansvarig för att övervaka framgångsrikt genomförande av säkerhetskopior av diskvolymer och ögonblicksbilder lagring. Microsoft ansvarar för att datareplikeringen utförs till platser för haveriberedskap.

**Användningsområden:** SAP-programinstanserna eller, när det gäller icke-SAP-program, programskiktet för dessa program. Ditt ansvar omfattar distribution, administration, drift och övervakning av dessa program. Du ansvarar för kapacitetsplanering av cpu-resursförbrukning, minnesförbrukning, Azure Storage-förbrukning och nätverksbandbreddsförbrukning i virtuella nätverk. Du ansvarar också för kapacitetsplanering för resursförbrukning från virtuella nätverk till SAP HANA på Azure (stora instanser).

**WANs**: De anslutningar som du upprättar från lokala till Azure-distributioner för arbetsbelastningar. Alla kunder med HANA Large Instance använder Azure ExpressRoute för anslutning. Den här anslutningen är inte en del av SAP HANA på Azure (Large Instances) lösning. Du är ansvarig för installationen av den här anslutningen.

**Arkiv**: Du kanske föredrar att arkivera kopior av data med hjälp av dina egna metoder i lagringskonton. Arkivering kräver hantering, efterlevnad, kostnader och åtgärder. Du är ansvarig för att generera arkivkopior och säkerhetskopior på Azure och lagra dem på ett kompatibelt sätt.

Se [SLA för SAP HANA på Azure (Stora instanser)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Nästa steg**
- Referera [till SAP HANA-arkitektur (stora instanser) på Azure](hana-architecture.md)
