---
title: 'Azure ExpressRoute: Designa för hög tillgänglighet'
description: Den här sidan innehåller arkitekturrekommendationer för hög tillgänglighet när du använder Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076685"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Designa för hög tillgänglighet med ExpressRoute

ExpressRoute är utformat för hög tillgänglighet för att tillhandahålla privat nätverksanslutning till Microsoft-resurser i operatörsklass. Med andra ord finns det ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. För att maximera tillgängligheten bör även kund- och tjänsteleverantörssegmentet i din ExpressRoute-krets vara arkitektför hög tillgänglighet. I den här artikeln ska vi först undersöka överväganden om nätverksarkitektur för att skapa robust nätverksanslutning med hjälp av en ExpressRoute, så låt oss sedan titta på de finjusteringsfunktioner som hjälper dig att förbättra den höga tillgängligheten för din ExpressRoute-krets.


## <a name="architecture-considerations"></a>Hänsyn till arkitektur

Följande bild illustrerar det rekommenderade sättet att ansluta med hjälp av en ExpressRoute-krets för att maximera tillgängligheten för en ExpressRoute-krets.

 [![1]][1.]

För hög tillgänglighet är det viktigt att upprätthålla redundansen för ExpressRoute-kretsen i hela end-to-end-nätverket. Med andra ord måste du upprätthålla redundans inom det lokala nätverket och bör inte äventyra redundansen i tjänsteleverantörens nätverk. Att upprätthålla redundansen som minimum innebär att man undviker en enda punkt av nätverksfel. Att ha redundant strömförsörjning och kylning för nätverksenheterna kommer att ytterligare förbättra den höga tillgängligheten.

### <a name="first-mile-physical-layer-design-considerations"></a>Första milen fysiska lager design överväganden

 Om du avslutar både de primära och sekundära anslutningarna för en ExpressRoute-kretsar på samma CPE (Customer Premises Equipment) äventyrar du den höga tillgängligheten i ditt lokala nätverk. Om du konfigurerar både de primära och sekundära anslutningarna via samma port i en CPE (antingen genom att avsluta de två anslutningarna under olika undergränssnitt eller genom att slå samman de två anslutningarna i partnernätverket) tvingar du partnern att äventyra hög tillgänglighet på deras nätverkssegment också. Denna kompromiss illustreras i följande siffra.

[![2]][2]

Å andra sidan, om du avslutar de primära och sekundära anslutningarna för en ExpressRoute-kretsar på olika geografiska platser, kan du äventyra nätverksprestandan för anslutningen. Om trafiken aktivt belastningsutjämnas över de primära och sekundära anslutningarna som avslutas på olika geografiska platser, skulle potentiell betydande skillnad i nätverksfördröjning mellan de två banorna resultera i suboptimala nätverk Prestanda. 

Geo-redundant designöverväganden finns i [Designa för haveriberedskap med ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktiva aktiva anslutningar

Microsoft-nätverket är konfigurerat för att driva de primära och sekundära anslutningarna för ExpressRoute-kretsar i aktivt aktivt läge. Genom dina ruttannonser kan du dock tvinga de redundanta anslutningarna för en ExpressRoute-krets att fungera i aktivt passivt läge. Reklam mer specifika vägar och BGP AS-sökväg som väntar är de vanligaste teknikerna som används för att göra en väg att föredra framför den andra.

För att förbättra hög tillgänglighet rekommenderas att du använder båda anslutningarna för en ExpressRoute-krets i aktivt aktivt läge. Om du låter anslutningarna fungera i aktivt aktivt läge kommer Microsoft-nätverket att belastningsjämna trafiken över anslutningarna per flöde.

Om du kör de primära och sekundära anslutningarna för en ExpressRoute-krets i aktivt passivt läge riskerar båda anslutningarna att misslyckas efter ett fel i den aktiva sökvägen. De vanligaste orsakerna till fel på att byta över är bristen på aktiv hantering av den passiva anslutningen och passiv anslutning reklam inaktuella vägar.

Alternativt, kör de primära och sekundära anslutningarna för en ExpressRoute krets i aktivt aktivt läge, resulterar i endast ungefär hälften av flödena misslyckas och få omdirigeras, efter en ExpressRoute anslutningsfel. Aktivt aktivt läge kommer därför att avsevärt bidra till att förbättra tiden för att återställa (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT för Microsoft-peering 

Microsoft-peering är utformat för kommunikation mellan offentliga slutpunkter. Så vanliga privata slutpunkter är nätverksadressöversatta (NATed) med offentlig IP i kund- eller partnernätverket innan de kommunicerar via Microsoft-peering. Förutsatt att du använder både primära och sekundära anslutningar i aktivt aktivt läge, var och hur du NAT påverkar hur snabbt du återställer efter ett fel i en av ExpressRoute-anslutningarna. Två olika NAT-alternativ illustreras i följande bild:

[![3]][3]

I alternativ 1 används NAT efter att ha delat upp trafiken mellan de primära och sekundära anslutningarna för ExpressRoute. För att uppfylla nat-kraven används oberoende NAT-pooler mellan de primära och sekundära enheterna så att returtrafiken kommer fram till samma kantenhet genom vilken flödet gick ut.

I alternativ 2 används en gemensam NAT-pool innan trafiken delas mellan de primära och sekundära anslutningarna för ExpressRoute. Det är viktigt att göra skillnaden att den gemensamma NAT-poolen innan du delar upp trafiken inte innebär att införa enstaka felpunkt och därmed äventyra hög tillgänglighet.

Med alternativ 1, efter ett ExpressRoute-anslutningsfel, är möjligheten att nå motsvarande NAT-pool bruten. Därför måste alla trasiga flöden återupprättas antingen av TCP eller programlager efter motsvarande tidsgränsen för fönster. Om någon av NAT-poolerna används för att frontend någon av de lokala servrarna och om motsvarande anslutning skulle misslyckas, kan de lokala servrarna inte nås från Azure förrän anslutningen är fast.

Med alternativ 2 kan NAT nås även efter ett primärt eller sekundärt anslutningsfel. Därför kan själva nätverkslagret omdirigera paketen och hjälpa till att återställa snabbare efter felet. 

> [!NOTE]
> Om du använder NAT-alternativ 1 (oberoende NAT-pooler för primära och sekundära ExpressRoute-anslutningar) och mappar en port med en IP-adress från en av NAT-poolen till en lokal server, kan servern inte nås via ExpressRoute-kretsen när motsvarande anslutningen misslyckas.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Finjusterande funktioner för privat peering

Låt oss i det här avsnittet granska valfria (beroende på din Azure-distribution och hur känslig du är för MTTR) funktioner som hjälper till att förbättra hög tillgänglighet för din ExpressRoute-krets. Anta särskilt en zonmedveten distribution av ExpressRoute-gateways för virtuella nätverk och BFD (Bidirectional Forwarding Detection).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Tillgänglighetszonsmedvetna ExpressRoute-gateways för virtuella nätverk

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du väljer zon redundant Azure IaaS-distribution kanske du också vill konfigurera zon redundanta virtuella nätverksgateways som avslutar ExpressRoute privat peering. Mer information finns i [Om zonsanta virtuella nätverksgatewayer i Azure-tillgänglighetszoner][zone redundant vgw]. Information om hur du konfigurerar zon redundant virtuell nätverksgateway finns i [Skapa en zon redundant virtuell nätverksgateway i Azure-tillgänglighetszoner][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Förbättrad felidentifieringstid

ExpressRoute stöder BFD över privat peering. BFD minskar identifieringstiden för fel över Layer 2-nätverket mellan Microsoft Enterprise Edge (MSEEs) och deras BGP-grannar på den lokala sidan från cirka 3 minuter (standard) till mindre än en sekund. Snabbfelsidentifieringstid hjälper till att påskynda återställning av fel. Mer information finns i [Konfigurera BFD via ExpressRoute][BFD].

## <a name="next-steps"></a>Nästa steg

I den här artikeln diskuterade vi hur man utformar för hög tillgänglighet av en ExpressRoute-kretsanslutning. En ExpressRoute-krets peering-punkt är fäst vid en geografisk plats och kan därför påverkas av katastrofala fel som påverkar hela platsen. 

Designöverväganden om hur du skapar geouppsagbar nätverksanslutning till Microsofts stamnät som tål katastrofala fel, vilket påverkar en hel region, finns [i Designa för haveriberedskap med ExpressRoute privat peering][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Rekommenderat sätt att ansluta med ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal sista mil anslutning"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-alternativ"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




