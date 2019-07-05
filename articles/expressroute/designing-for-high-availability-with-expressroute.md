---
title: Utforma för hög tillgänglighet med Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller arkitektoniska rekommendationer för hög tillgänglighet när du använder Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466654"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Utforma för hög tillgänglighet med ExpressRoute

ExpressRoute är utformat för hög tillgänglighet för operatör i företagsklass privat nätverksanslutningar till Microsoft-resurser. Det finns alltså ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. För att maximera tillgängligheten, bör kunden och service provider-segmentet i ExpressRoute-kretsen också vara byggts för hög tillgänglighet. I den här artikeln, först ska vi titta på Nätverksöverväganden arkitektur för att skapa stabil nätverksanslutning med hjälp av en ExpressRoute och sedan ska vi titta på de justera funktioner som hjälper dig att förbättra tillgängligheten för ExpressRoute-kretsen.


## <a name="architecture-considerations"></a>Överväganden för arkitektur

Följande bild visar det rekommenderade sättet att ansluta med hjälp av en ExpressRoute-krets för att maximera tillgängligheten för en ExpressRoute-krets.

 [![1]][1]

För hög tillgänglighet är det mycket viktigt att bibehålla redundans för ExpressRoute-kretsen i hela slutpunkt till slutpunkt-nätverket. Med andra ord du behöver underhålla redundans i ditt lokala nätverk och bör inte påverka redundans inom tjänstleverantörsnätverket. Underhålla redundans på minst innebär att undvika felkritisk nätverksfel. Med redundant strömförsörjning och kylning för det virtuella nätverket ytterligare enheter kommer att förbättra tillgängligheten.

### <a name="first-mile-physical-layer-design-considerations"></a>Designöverväganden för första mil fysiska lagret

 Om du säger upp både primära och sekundära anslutningar för en ExpressRoute-kretsar på samma kund lokal utrustning (CPE), är du att kompromissa med hög tillgänglighet i ditt lokala nätverk. Om du konfigurerar både primära och sekundära anslutningar via samma port för en CPE (antingen genom att terminera två anslutningar under olika undergränssnitt eller genom att sammanfoga två anslutningar inom för partner network) kan tvingar du dessutom partnern att kompromettera hög tillgänglighet i sina nätverkssegment. Det här angreppet illustreras i följande bild.

[![2]][2]

Å andra sidan, om du avslutar primärt och sekundära anslutningar för en ExpressRoute-kretsar i olika geografiska platser, sedan du kan vara att kompromissa med nätverksprestanda för anslutningen. Om trafiken är aktivt belastningsutjämnas mellan primärt och sekundära anslutningar som avslutas på olika geografiska platser, skulle potentiella betydande skillnad i Nätverksfördröjningen mellan de två sökvägarna resultera i icke-optimal nätverk prestanda. 

Designöverväganden för geo-redundant, se [utformning för katastrofåterställning med ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktiv-aktiv-anslutningar

Microsoft-nätverket är konfigurerat för att driva de primära och sekundära anslutningarna för ExpressRoute-kretsar i aktivt-aktivt läge. Via din vägannonseringar kan du tvinga redundanta anslutningar för en ExpressRoute-krets för att fungera i aktivt-passivt läge. Annonserar mer specifika vägar och BGP eftersom path-prepending är vanliga tekniker som används för att göra en sökväg som är att föredra jämfört med den andra.

För att förbättra tillgängligheten, rekommenderar vi för att fungera för båda anslutningarna för en ExpressRoute-krets i aktivt-aktivt läge. Om du låter anslutningarna fungerar i aktivt-aktivt läge kommer Microsoft network belastningsutjämna trafiken mellan anslutningarna per flöde.

Kör de primära och sekundära anslutningarna i en ExpressRoute-krets i aktivt-passivt läge ansikte risken för båda anslutningarna misslyckas efter ett fel i den aktiva sökvägen. Vanliga orsaker till fel om hur du växlar är brist på aktiva hanteringen av passiv anslutning och passiv anslutning annonserar inaktuella vägar.

Du kan också köra primära och sekundära anslutningar i en ExpressRoute-krets i aktivt-aktivt läge resulterar i endast ungefär en halv flöden misslyckas och komma omdirigeras efter en ExpressRoute-anslutningsfel. Därför kan avsevärt aktivt-aktivt läge förbättra den tid att återställa (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT för Microsoft-peering 

Microsoft-peering är utformat för kommunikation mellan offentliga slutpunkter. Så ofta är lokala privata slutpunkter Network adress översättas (NATed) med offentliga IP-adresser på kund eller partner network innan de kommunicerar via Microsoft-peering. Under förutsättning att du använder både primära och sekundära anslutningar i aktivt-aktivt läge, var och hur du NAT har en inverkan på hur snabbt du återställa efter ett fel i en ExpressRoute-anslutningar. Två olika NAT-alternativ visas i följande bild:

[![3]][3]

I alternativ 1 tillämpas NAT sedan trafiken mellan primära och sekundära anslutningar expressroute. För att uppfylla de tillståndskänsliga NAT-krav, används oberoende NAT-pooler mellan primärt och sekundära enheterna så att Returtrafiken skulle tas emot på samma edge-enhet som flödet egressed.

I alternativ 2 används en gemensam NAT-pool innan du delar trafiken mellan primära och sekundära anslutningar expressroute. Det är viktigt att skilja betyder vanliga NAT-pool innan du delar trafiken inte introduktion till enskild felpunkt vilket att kompromissa med hög tillgänglighet.

Med alternativ 1, följa en ExpressRoute-anslutningsfel har möjlighet att nå den motsvarande NAT-poolen brutits. Därför alla bruten flöden måste vara har återupprättats antingen TCP eller programnivån följa motsvarande timeout för fönstret. Om något av NAT-pooler är brukade klientdel någon av de lokala servrarna och om motsvarande anslutningen misslyckas, lokala servrar kan inte nås från Azure tills anslutningen har åtgärdats.

Med alternativ 2 är NAT kan nås även efter en primär eller sekundär anslutningsfel. Därför nätverksnivån själva kan dirigera om paket och hjälper dig att snabbare återställning efter fel. 

> [!NOTE]
> Om du använder NAT-alternativ 1 (oberoende NAT-pooler för primära och sekundära ExpressRoute-anslutningar) och mappar en port från en IP-adress från en NAT-pool till en lokal server kan servern inte kan nås via ExpressRoute-krets när motsvarande Det går inte att upprätta anslutningen.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Finjustera funktioner för privat peering

I det här avsnittet, kan vi granska valfritt (beroende på din Azure-distribution och hur känslig är att MTTR) funktioner som hjälp till att förbättra tillgängligheten för ExpressRoute-kretsen. Nu ska vi granska mer specifikt zon distribution av virtuella nätverksgatewayer för ExpressRoute och dubbelriktad vidarebefordran identifiering (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Tillgänglighetszon medveten ExpressRoute virtuella nätverksgatewayer

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du väljer för zonredundant Azure IaaS-distribution kan du även vill konfigurera zonredundant virtuella nätverksgatewayer som avslutar ExpressRoute privat peering. Läs mer i [om zonredundant virtuella nätverksgatewayer i Azure Availability Zones][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Förbättra tidpunkten för identifieringen av fel

ExpressRoute stöder BFD över privat peering. BFD minskar tidpunkten för identifieringen av fel i Layer 2-nätverket mellan Microsoft Enterprise-kant (msee) och deras BGP grannar på den lokala sidan från ca 3 minuter (standard) till mindre än en sekund. Snabba fel vid tidpunkten för identifieringen hjälper hastening felåterställning. Läs mer i [konfigurera BFD via ExpressRoute][BFD].

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs vi hur du utformar för hög tillgänglighet för en ExpressRoute-krets-anslutningen. En ExpressRoute-krets, peering tidpunkt är fäst på en geografisk plats och därför kan påverkas av oåterkalleligt fel som påverkar hela platsen. 

Designöverväganden för att skapa geo-redundant nätverksanslutning till Microsoft-stamnät som klarar att kritiska fel som påverkar en hel region, se [utformning för katastrofåterställning med ExpressRoute privat peering][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "rekommenderade sätt att ansluta med ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal senaste mil anslutning"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-alternativ"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




