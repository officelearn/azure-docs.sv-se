---
title: 'Platser och anslutningsleverantörer: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter plats.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: pareshmu
ms.openlocfilehash: 4750898b138a726cac15aa7a0e2b8863c46d4ee8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser

> [!div class="op_single_selector"]
> * [Platser efter leverantör](expressroute-locations.md)
> * [Leverantörer efter plats](expressroute-locations-providers.md)


Tabellerna i den här artikeln innehåller information om ExpressRoute-anslutningsleverantörer, ExpressRoutes geografiska täckning, Microsoft-molntjänster som stöds via ExpressRoute och ExpressRoutes systemintegratörer (SI).

## <a name="partners"></a>Anslutningsproviders för ExpressRoute
ExpressRoute stöds i alla Azures regioner och platser. Följande karta innehåller en lista med Azures regioner och ExpressRoute-platser. ExpressRoute-platser avser platser där Microsoft peerkopplas med flera tjänstleverantörer.

![Platskarta][0]

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Azure-regioner till ExpressRoute-platser inom en geopolitisk region
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Nordamerika** |Östra USA, västra USA, östra USA 2, västra USA 2, centrala USA, södra centrala USA, norra centrala USA, västra centrala USA, centrala Kanada, östra Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Washington DC, Montreal, Quebec City, Toronto |
| **Sydamerika** |Södra Brasilien |Sao Paulo |
| **Europa** |Frankrike (centrala), Frankrike (södra), Europa (norra), Europa (västra), Storbritannien (västra), Storbritannien (södra) |Amsterdam, Dublin, London, Newport (Wales), Paris |
| **Asien** |Östra Asien, Sydostasien |Hongkong, Singapore, Singapore2 |
| **Japan** |Västra Japan, östra Japan |Osaka, Tokyo |
| **Australien** |Sydöstra Australien, östra Australien |Melbourne, Sydney |
| **Indien** |Västra Indien, centrala Indien, södra Indien |Chennai, Mumbai |
| **Sydkorea** |Centrala Korea, Sydkorea |Busan, Söul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |Iowa (USA-förvaltad region), Virginia (USA-förvaltad region), US DoD centrala, US DoD, östra  |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **Kina** |Norra Kina, östra Kina |Beijing, Shanghai |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="locations"></a>Platser för anslutningsleverantörer

Följande tabell innehåller anslutningsplatser och tjänstleverantörerna för varje plats. Om du vill visa leverantörer och de platser där de kan tillhandahålla service går du till [Platser efter tjänsteleverantör](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Produktions-Azure
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Amsterdam** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions – Cloud Connect, Interxion, KPN, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Verizon, Zayo |
| **Atlanta** |Equinix |
| **Busan** |LG CNS |
| **Chennai** | Airtel+, Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chicago** |AT&T NetBond, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Verizon, Zayo |
| **Dallas** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Telmex Uninet+, Verizon, Zayo|
| **Denver** |CoreSite, Megaport |
| **Dublin** |Colt, eir, Interxion, Megaport, Telecity Group |
| **Hong Kong** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Las Vegas** |Level 3 Communications, Megaport |
| **London** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse - KDDI, Telenor, Verizon, Vodafone, Zayo |
| **Los Angeles** |CoreSite, Equinix, Megaport, NTT, Zayo |
| **Melbourne** |AARNet, Equinix, Megaport, NEXTDC, Optus+, Telstra Corporation |
| **Miami** |C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** |Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** |Airtel+, Global CloudXchange (GCX), Sify, Tata Communications |
| **New York** |CenturyLink Cloud Connect, Coresite, Equinix, Megaport, Zayo |
| **Newport(Wales)** |Level 3 Communications, Next Generation Data |
| **Osaka** |Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** |Colt, Intercloud, Interxion, Equinix, Orange |
| **Quebec City** | Bell Canada, Megaport |
| **San Antonio** |CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** |Ascenty Data Centers+, Equinix, Level 3 Communications, Neutrona Networks, Telefonica, UOLDIVEO |
| **Seattle** |Equinix, Level 3 Communications, Megaport |
| **Söul** |KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, PacketFabric, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |
| **Singapore** |Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Singapore2** |Megaport, SingTel |
| **Sydney** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, Verizon |
| **Tokyo** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** |AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Console, Equinix, Megaport, Telus, Zayo |
| **Washington DC** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Sprint, Tata Communications, Verizon, Zayo |

 **+** kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, Level 3 Communications+, Verizon |
| **Silicon Valley** | Equinix, Level 3 Communications |
| **Seattle** | Equinix |
| **Washington DC** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Kina
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Peking** |China Telecom |
| **Shanghai** |China Telecom |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Berlin** |Colt+, e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Anslutningar via Exchange-leverantörer
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="c1partners"></a>Anslutning via ytterligare leverantörer
| **Plats** | **Exchange** | **Anslutningsproviders** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, Eurofiber, Fastweb S.p.A, MainOne, Nianet, Telia |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | BICS, Nianet, QSC AG |
| **Hong Kong** | Equinix | Macroview Telecom |
| **London** | Equinix euNetworks, Telecity | Bezeq International Ltd., Epsilon, Exponential E, HSO, NexGen Networks, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Windstream |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Gtt Communications Inc, Epsilon, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Kontinent** | **Systemintegratörer** |
| --- | --- |
| **Asien** |Avanade Inc., OneAs1a |
| **Australien** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Nordamerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Sydamerika** |Avanade Inc. |
## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
