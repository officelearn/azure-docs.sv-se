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
ms.date: 09/06/2019
ms.author: cherylmc
ms.openlocfilehash: f864384dd5b86420fd9edf446b2573421a05f807
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801502"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser

> [!div class="op_single_selector"]
> * [Platser efter leverantör](expressroute-locations.md)
> * [Leverantörer efter plats](expressroute-locations-providers.md)


Tabellerna i den här artikeln innehåller information om ExpressRoute geografisk täckning och platser, ExpressRoute Connectivity-providers och ExpressRoute system integrators (SIs).

> [!Note]
> Azure-regioner och ExpressRoute-platser är två distinkta och olika koncept, så att skillnaden mellan de två är avgörande för att utforska Azure Hybrid nätverks anslutning. 
>
>

## <a name="azure-regions"></a>Azure-regioner
Azure-regioner är globala data Center där Azure Compute, nätverk och lagrings resurser finns. När du skapar en Azure-resurs måste kunden välja en resurs plats. Resurs platsen avgör vilken Azure-datacenter (eller tillgänglighets zon) som resursen skapas i.

## <a name="expressroute-locations"></a>ExpressRoute-platser
ExpressRoute-platser (kallas ibland peering-platser eller uppfyller platser) är samplacerings anläggningar där Microsoft Enterprise Edge-enheter (MSEE: N) finns. ExpressRoute platser är start punkten till Microsofts nätverk – och distribueras globalt, vilket ger kunderna möjlighet att ansluta till Microsofts nätverk runtom i världen. Dessa platser är där ExpressRoute partners och ExpressRoute Direct-kunder utfärdar kors anslutningar till Microsofts nätverk. I allmänhet behöver ExpressRoute-platsen inte matcha Azure-regionen. En kund kan till exempel skapa en ExpressRoute-krets med resurs platsen *östra USA*, på peer-platsen i *Seattle* .

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen. 

## <a name="locations"></a>Azure-regioner till ExpressRoute-platser inom en regional politisk region
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Zon** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- | --- |
| **Australiensiska myndigheter** | 1 | Centrala Australien, Centrala Australien 2 |Canberra, Canberra2 |
| **Europa** | 1 |Frankrike, centrala; Frankrike, södra; Europa, norra; Europa, västra; Storbritannien, västra; Storbritannien, södra |Amsterdam, Amsterdam2, köpen hamn, Dublin, Frankfurt, London, London2, Marseille, Newport (Wales), Paris, Stockholm, Zürich |
| **Nordamerika** | 1 |USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asien** | 2 |Östra Asien, Sydostasien |Hong Kong SAR, Kuala Lumpur, Singapore, Singapore2, Taipei, |
| **Indien** | 2 |Västra Indien, centrala Indien, södra Indien |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Västra Japan, östra Japan |Osaka, Tokyo |
| **Oceanien** | 2 |Sydöstra Australien, östra Australien |Auckland, Melbourne, Perth, Sydney | 
| **Sydkorea** | 2 |Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **FÖRENADE ARABEMIRATEN** | 3 | Förenade Arabemiraten Central, Förenade Arabemiraten Nord | Dubai, Dubai2 |
| **Sydafrika** | 3 |Sydöstra Sydafrika, norra Sydafrika |Kapstaden, Johannesburg |
| **Sydamerika** | 3 |Södra Brasilien |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regioner och politiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; US Gov, Iowa; US Gov, Texas; US Gov, Virginia; US DoD, centrala; US DoD, östra  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="partners"></a>Anslutningsproviders för ExpressRoute

Följande tabell innehåller anslutningsplatser och tjänstleverantörerna för varje plats. Om du vill visa leverantörer och de platser där de kan tillhandahålla service går du till [Platser efter tjänsteleverantör](expressroute-locations.md). 

**Lokala Azure-regioner** är de som [ExpressRoute lokala](expressroute-faqs.md) på varje peering-plats kan komma åt. **n/a** anger att ExpressRoute Local inte är tillgängligt på peering-platsen.


### <a name="production-azure"></a>Produktions-Azure
| **Location** | **Ägare och adress** | **Lokala Azure-regioner** | **Tjänstleverantörer** |
| --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | Västra Europa | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | Västra Europa | CenturyLink Cloud Connect, CIX, Colt, Interxion, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | Saknas | Equinix, Megaport |
| **Auckland** | [Vocus-grupp NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | Saknas | Devoli, Kordia, Megaport, Spark NZ, Vocus-grupp NZ |
| **Busan** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | Sydkorea, södra | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australien, centrala | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australien, centrala 2| CDC |
| **Kapstaden** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | Sydafrika, västra | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Indien, södra | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Indien, södra | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Norra centrala USA | Aryaka Networks, på & T netobligation, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW global Limited, Sprint, Telia-bärvåg, Verizon, Zayo |
| **Hamn** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | Saknas | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | Saknas | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | Västra centrala USA | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | Förenade Arabemiraten, norra | Etisalat Förenade Arabemiraten |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | Förenade Arabemiraten, norra | du datamena, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | Norra Europa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | Saknas | DE-CIX, Interxion, orange |
| **Hong Kong SAR** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | Östasien | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, Kina Telecom global, Equinix, Megaport, NTT Communications, orange, PCCW global Limited, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | Sydafrika, norra | Brittiska telekom, Internet-lösningar – moln anslutning, flytande telekom, orange, Teraco |
| **Kuala Lumpur** | [TID för dotCom Menara-mål](https://www.aims.com.my/co-location/points-of-presence.html) | Saknas | TIME dotCom |
| **Las Vegas** | [Byt LV](https://www.switch.com/las-vegas) | Saknas | CenturyLink Cloud Connect, Megaport |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | Storbritannien, södra | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Östra huset, norra två](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | Storbritannien, södra | IX räckvidd, Equinix |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | Saknas | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | Frankrike, södra | CIX, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | Sydöstra Australien | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | Saknas | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | Saknas | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | Indien, västra | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Indien, västra | Airtel, Sify, Vodafone Idea |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | Saknas | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Nästa generations brandvägg](https://www.nextgenerationdata.co.uk) | Storbritannien, västra | Brittiska telekom, Colt, nivå 3-kommunikation, nästa generations data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | Västra Japan | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | Frankrike, centrala | CenturyLink Cloud Connect, Colt, Equinix, intercloud, Interxion, orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | Saknas | Megaport, NextDC |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | Östra Kanada | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | Södra centrala USA | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | Södra Brasilien | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Västra USA 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Söul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | Sydkorea, centrala | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | Västra USA | Aryaka Networks, på & T netobligation, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, intercloud, Internet2, IX Reach, paket, PacketFabric, Level 3 Communications, Megaport, orange, Sprint, Tata kommunikation, Telia-bärvåg, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | Västra USA | Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | Sydostasien | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global växel i Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | Sydostasien | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | Saknas | Telia-bärvåg |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | Östra Australien | AARNet, på & T netobligation, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus-grupp NZ |
| **Taipei** | Chief Telecom | Saknas | Chief Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | Östra Japan | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | Centrala Kanada | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | Östra USA, östra USA 2 | Aryaka Networks, på & T netobligation, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, intercloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, orange, PacketFabric, SES, Sprint, Tata Kommunikation, Telia-bärvåg, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | Östra USA, östra USA 2 |Coresite, Viasat | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | Saknas | Mellan moln, Interxion |

 **+** kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Location** | **Tjänstleverantörer** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kina
| **Location** | **Tjänstleverantörer** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Kina, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | Kina, GDS |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland
| **Location** | **Tjänstleverantörer** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Anslutning via Exchange-leverantörer
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

## <a name="c1partners"></a>Anslutning via ytterligare tjänst leverantörer
| **Location** | **Exchange** | **Anslutnings leverantörer** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, nivå 3-kommunikation, stad | BICS, CloudXpress, euro fiber, Fastweb S. p. A, Mellanöstern Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST telekom Luxembourg, Proximus, TDC Erhverv, telekom Italia gnistrande, telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Kron Castle
| **Kapstaden** | Teraco | MTN |
| **Chicago** | Equinix| Kron Castle, spektrum Enterprise, windstream |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Telecom, Cox Business, kron Castle, data Funna, spektrum Enterprise, Transtelco |
| **Frankfurt** | Interxion, stad | BICS, Cinia, Nianet, QSC AG, telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hong Kong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon, Epsilon-begränsad, exponentiell E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Kron Castle, spektrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, kron Castle, spektrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon, Epsilon Limited, LGA telekom, United information motorväg (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Luftgrinds teknikerna Inc., Beanfield MetroConnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, kron Castle, GTT Communications Inc, Epsilon-Limited Limited, Masergy, windstream |

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
