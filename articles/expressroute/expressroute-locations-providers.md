---
title: 'Platser och anslutningsproviders: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter plats.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2019
ms.author: pareshmu
ms.openlocfilehash: 8253610bd0c329c952442044586236acc56d4957
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978093"
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

| **Geopolitisk region** | **Zon** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- | --- |
| **Australiensiska myndigheter** | 1 | Centrala Australien, Centrala Australien 2 |Canberra, Canberra2 |
| **Europa** | 1 |Frankrike, centrala; Frankrike, södra; Europa, norra; Europa, västra; Storbritannien, västra; Storbritannien, södra |Amsterdam, Amsterdam2, Dublin, Frankfurt, London, London2, Marseille, Newport(Wales), Paris, Zurich |
| **Nordamerika** | 1 |USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asien** | 2 |Östra Asien, Sydostasien |Hongkong SAR, Kuala Lumpur, Singapore, Singapore2 Taipei |
| **Australien** | 2 |Sydöstra Australien, östra Australien |Melbourne, Perth, Sydney | 
| **Indien** | 2 |Västra Indien, centrala Indien, södra Indien |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Västra Japan, östra Japan |Osaka, Tokyo |
| **Sydkorea** | 2 |Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **UAE** | 3 | Förenade Arabemiraten, centrala, Förenade Arabemiraten, Norra | Dubai, Dubai2 |
| **Sydafrika** | 3 |Sydafrika, västra, Sydafrika, Norra |Kapstaden, Johannesburg |
| **Sydamerika** | 3 |Brasilien, södra |Sao Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; US Gov, Iowa; US Gov, Texas; US Gov, Virginia; US DoD, centrala; US DoD, östra  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="locations"></a>Platser för anslutningsleverantörer

Följande tabell innehåller anslutningsplatser och tjänstleverantörerna för varje plats. Om du vill visa leverantörer och de platser där de kan tillhandahålla service går du till [Platser efter tjänsteleverantör](expressroute-locations.md#locations). 

**Lokala Azure-regioner** är de som [ExpressRoute lokal](expressroute-faqs.md) i varje peeringplats kan komma åt. **Ej tillämpligt** innebär att ExpressRoute lokal inte är tillgängliga där peering.


### <a name="production-azure"></a>Produktions-Azure
| **Plats** | **Peering-platsägare** | **Lokal Azure-regioner** | **Tjänstleverantörer** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europa, västra | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europa, västra | DE-CIX, Interxion |
| **Atlanta** | Equinix | Saknas | Equinix, Megaport |
| **Busan** |LG CNS | Sydkorea, södra | LG CNS |
| **Canberra** | CDC | Australien, centrala | CDC |
| **Canberra2** | CDC | Australien, centrala 2| CDC |
| **Kapstaden** | Teraco | Sydafrika, västra | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Indien, södra | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Indien, södra | Airtel |
| **Chicago** | Equinix | USA, norra centrala | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Dallas** | Equinix | Saknas | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Verizon, Zayo|
| **Denver** | CoreSite | USA, västra centrala  | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat Förenade Arabemiraten | Förenade Arabemiraten, norra | Etisalat Förenade Arabemiraten |
| **Dubai2** | du datamena | Förenade Arabemiraten, norra | du datamena |
| **Dublin** | Equinix | Europa, norra | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | Saknas | DE-CIX, Interxion |
| **Hongkong SAR** | Equinix | Asien, östra | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Sydafrika, norra | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | Saknas | TIME dotCom |
| **Las Vegas** | Switch | Saknas | CenturyLink Cloud Connect, Megaport |
| **London** | Equinix | Södra Storbritannien | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Södra Storbritannien | IX räckvidd, Equinix |
| **Los Angeles** | CoreSite | Saknas | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | Frankrike, södra | Interxion |
| **Melbourne** | NextDC | Australien, sydöstra | AARNet, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | Saknas | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | Saknas | Bell Canada, Cologix, Telus, Zayo |
| **Mumbai** | Tata Communications | Indien, västra | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, |
| **Mumbai2** | Airtel | Indien, västra | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | Saknas | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | Nästa generations data | Västra Storbritannien | British Telecom, Level 3 Communications, Next Generation Data |
| **Osaka** | Equinix | Japan, västra | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | Interxion | Centrala Frankrike | Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | Saknas | Megaport, NextDC |
| **Quebec City** | 4Degrees | Kanada, östra | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | USA, södra centrala | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brasilien, södra | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | USA, västra 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Zayo |
| **Söul** | KINX | Sydkorea, centrala | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | USA, västra | Aryaka Networks, AT & T NetBond, British Telecom CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, IX nå, paket, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Verizon, Zayo |
| **Silicon Valley2** | Coresite | USA, västra | Coresite | 
| **Singapore** | Equinix | Sydostasien | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | Global Switch | Sydostasien | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Sydney** | Equinix | Australien, östra | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Taipei** | Chief Telecom | Saknas | Chief Telecom, FarEasTone |
| **Tokyo** | Equinix | Japan, östra | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada, centrala | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Zayo |
| **Washington DC** | Equinix | Östra USA, östra USA 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona nätverk, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia operatör Verizon, Zayo |
| **Washington DC2** | Coresite | Östra USA, östra USA 2 |Coresite | 
| **Zürich** | Interxion | Saknas | Interxion |

 **+** kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kina
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | GDS |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland
| **Plats** | **Tjänstleverantörer** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Anslutningar via Exchange-leverantörer
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="c1partners"></a>Anslutning via ytterligare leverantörer
| **Plats** | **Exchange** | **Anslutningsproviders** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telia |
| **Kapstaden** | Teraco | MTN |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks, Telecity | Bezeq AB, CoreAzure, Epsilon telekommunikation Limited, exponentiellt E, HSO, NexGen nätverk, Proximus, Tamares telekom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Equinix | Cox Business, Windstream |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies, Inc. Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Gtt Communications Inc, Epsilon Telecommunications Limited, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Kontinent** | **Systemintegratörer** |
| --- | --- |
| **Asien** |Avanade Inc., OneAs1a |
| **Australien** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Nordamerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Sydamerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
