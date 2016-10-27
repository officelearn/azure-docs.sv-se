<properties
   pageTitle="ExpressRoute-platser | Microsoft Azure"
   description="Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="cherylmc" />


# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser

Tabellerna i den här artikeln innehåller information om ExpressRoute-anslutningsleverantörer, ExpressRoutes geografiska täckning, Microsoft-molntjänster som stöds via ExpressRoute och ExpressRoutes systemintegratörer (SI).

## <a name="<a-name="partners"></a>expressroute-connectivity-providers"></a><a name="partners"></a>Anslutningsproviders för ExpressRoute

ExpressRoute stöds i alla Azures regioner och platser. Följande karta innehåller en lista med Azures regioner och ExpressRoute-platser. ExpressRoute-platser avser platser där Microsoft peerkopplas med flera tjänstleverantörer.

![Platskarta][0]

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen. Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

|**Geopolitisk region**|**Azure-regioner**|**ExpressRoute-platser**|
|---|---|---|
|**Nordamerika**|Östra USA, västra USA, östra USA 2, centrala USA, södra centrala USA, norra centrala USA, centrala Kanada, östra Kanada|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto|
|**Sydamerika**|Södra Brasilien|Sao Paulo|
|**Europa**|Norra Europa, Västra Europa, Västra Storbritannien, Södra Storbritannien|Amsterdam, Dublin, London, Newport(Wales)+, Paris|
|**Asien**|Östra Asien, Sydostasien|Hongkong, Singapore|
|**Japan**|Västra Japan, östra Japan|Osaka, Tokyo|
|**Australien**|Sydöstra Australien, östra Australien|Melbourne, Sydney|
|**Indien**|Västra Indien, centrala Indien, södra Indien|Chennai, Mumbai|



Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

|**Geopolitisk region**|**Azure-regioner**|**ExpressRoute-platser**|
|---|---|---|---|
|**Moln för amerikanska myndigheter**|Iowa (USA-förvaltad region), Virginia (USA-förvaltad region)|Chicago, Dallas, New York, Washington DC|
|**Kina**|Norra Kina, östra Kina|Beijing, Shanghai|
|**Tyskland**|Centrala Tyskland, östra Tyskland|Berlin, Frankfurt|


Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.


## <a name="connectivity-provider-locations"></a>Anslutningsleverantörens platser

> [AZURE.SELECTOR]
[Platser efter leverantör](expressroute-locations.md#connectivity-provider-locations)
[Leverantörer efter plats](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Produktions-Azure
| **Plats**  | **Tjänstleverantörer** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Tata Communications |
| **Chicago** | AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **Dallas** | AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport |
| **Dublin** | Colt, TeleCity Group |
| **Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **London** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Level 3 Communications+, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** | Equinix, Megaport, Zayo Group |
| **Montreal** | Cologix+ |
| **Mumbai** | Tata Communications |
| **Osaka** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Paris** | Interxion |
| **Sao Paulo** | Equinix, Telefonica |
| **Seattle** | Equinix, Level 3 Communications, Megaport |
| **Silicon Valley** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Singapore** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** | AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |

 **+** kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

#### <a name="us-government-cloud"></a>U.S. Government-moln

| **Plats**  |**Tjänstleverantörer** |
|---------------|--------------------|
| **Chicago** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |  Equinix, Verizon+ |
| **New York** | Equinix, Level 3 Communications+, Verizon |
| **Washington DC** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |

#### <a name="china"></a>Kina

| **Plats**  | **Tjänstleverantörer** |
|---------------|-----------------------|
| **Peking** | China Telecom |
| **Shanghai** |  China Telecom |
Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Tyskland

| **Plats**  | **Tjänstleverantörer** |
|---------------|-----------------------|
| **Berlin** | Colt, e-shelter |
| **Frankfurt** | Colt, Equinix, Interxion |

## <a name="<a-name="nonpartners"></a>connectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Anslutning för leverantörer som inte listas

Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

- Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
    - Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
- Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
    - Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

|**Plats**|**Exchange**|**Anslutningsproviders**|
|-------------|------------|-------------------------|
| **New York** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska Communications |
| **Silicon Valley** | Equinix | XO Communications |
| **Singapore** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer

Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

|**Kontinent**|**Systemintegratörer**|
|-------------|---------------------|
| **Asien** | Avanade Inc., OneAs1a|
| **Europa** | Avanade Inc., Dotnet Solutions|
| **USA** | Avanade Inc., Equinix Professional Services, Perficient, Project Leadership|

## <a name="next-steps"></a>Nästa steg

- Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
- Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"



<!--HONumber=Oct16_HO2-->


