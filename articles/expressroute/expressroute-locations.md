---
title: "Anslutningsleverantörer och platser: Azure ExpressRoute | Microsoft Docs"
description: "Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter anslutningsleverantör."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 70955113bbd2e008be855e591d04651d09438e81
ms.lasthandoff: 03/24/2017


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

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Azure-regioner till ExpressRoute-platser inom en geopolitisk region.
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Nordamerika** |Östra USA, västra USA, östra USA 2, västra USA 2, centrala USA, södra centrala USA, norra centrala USA, västra centrala USA, centrala Kanada, östra Kanada |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal, Quebec City, Toronto |
| **Sydamerika** |Södra Brasilien |Sao Paulo |
| **Europa** |Norra Europa, Västra Europa, Västra Storbritannien, Södra Storbritannien |Amsterdam, Dublin, London, Newport (Wales), Paris |
| **Asien** |Östra Asien, Sydostasien |Hongkong, Singapore |
| **Japan** |Västra Japan, östra Japan |Osaka, Tokyo |
| **Australien** |Sydöstra Australien, östra Australien |Melbourne, Sydney |
| **Indien** |Västra Indien, centrala Indien, södra Indien |Chennai, Mumbai |
| **Sydkorea** |Centrala Korea, Sydkorea |Busan, Söul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- | --- |
| **Moln för amerikanska myndigheter** |Iowa (USA-förvaltad region), Virginia (USA-förvaltad region), US DoD centrala, US DoD, östra  |Chicago, Dallas, New York, Silicon Valley, Washington DC |
| **Kina** |Norra Kina, östra Kina |Beijing, Shanghai |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="locations"></a>Platser för anslutningsleverantörer

I följande tabell visas platser efter tjänstleverantör. Om du vill visa tillgängliga leverantörer efter plats går du till [Tjänsteleverantörer efter plats](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Produktions-Azure
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365 och CRM Online** | **Platser** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Stöds |Stöds |Melbourne, Sydney |
| **Airtel** | Kommer snart | Kommer snart | Chennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Stöds |Stöds |Amsterdam, Dallas, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Stöds |Stöds |Montreal, Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Stöds |Stöds |Amsterdam, Hong Kong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Kommer snart |Kommer snart |Silicon Valley |
| **China Telecom Global** |Stöds |Stöds inte |Hongkong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Stöds |Stöds |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds |Amsterdam, Dublin, London, Tokyo |
| **Comcast** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **Konsol**| Stöds | Stöds |Silicon Valley, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Stöds |Stöds |Los Angeles, New York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, Osaka, Paris+, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Stöds |Stöds |Amsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Stöds| Stöds | Chennai |
| **GÉANT** |Stöds |Stöds |Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Stöds |Stöds |Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** |Stöds |Stöds |Amsterdam, London, Singapore, Washington DC |
| **Internet Solutions - Cloud Connect** |Stöds |Stöds |Amsterdam, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Stöds |Stöds |Amsterdam, London, Paris |
| **Jisc** |Stöds |Stöds |London |
| **KINX** |Stöds |Stöds |Seoul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Stöds | Stöds | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Las Vegas+, London, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Kommer snart |Kommer snart |Busan + |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds |Stöds |Dallas, Hongkong, Las Vegas, Los Angeles, Melbourne, New York, Quebec City, Seattle, Singapore, Sydney, Toronto, Washington DC |
| **MTN** |Stöds |Stöds |London |
| **[Nästa datageneration](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Stöds |Stöds |Newport(Wales) |
| **NEXTDC** |Stöds |Stöds |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Stöds |Stöds |London, Los Angeles, Osaka, Singapore, Tokyo, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Stöds |Stöds |Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **PCCW Global Limited** |Stöds |Stöds |Hongkong |
| **Sejong Telecom** |Stöds |Stöds |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Stöds |Stöds |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Stöds |Stöds |Singapore |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Stöds |Stöds |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Stöds |Stöds |Amsterdam, Chennai, Hong Kong, London, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Stöds |Stöds |Amsterdam, Dublin, London |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Stöds |Stöds |Amsterdam+, Sao Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Stöds |Stöds |London |
| **Telenor** |Stöds |Stöds |Amsterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Hongkong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Stöds |Stöds inte |London |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Stöds |Stöds |Chicago, Dallas+, London+, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** kommer snart

### <a name="national-cloud-environment"></a>Nationell molnmiljö

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Chicago, Dallas, New York, Seattle+, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Chicago, New York+, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds | Stöds | Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Stöds |Stöds |Chicago, Dallas, New York, Washington DC |

### <a name="china"></a>Kina
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **China Telecom** |Stöds |Stöds inte |Beijing, Shanghai |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds inte |Berlin+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds inte |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Stöds |Stöds inte |Berlin |
| **Interxion** |Stöds |Stöds inte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds  | Stöds inte | Berlin |

## <a name="c1partners"></a>Anslutning för leverantörer som inte listas
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

| **Anslutningsleverantör** | **Exchange** | **Platser** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapore |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokyo |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Singapore |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponentiellt E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |New York, Washington DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion  London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Silicon Valley, Washington DC |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |


## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Systemintegrator** | **Kontinent** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Asien, Europa, Nordamerika, Sydamerika |
| **Bright Skies GmbH** | Europa
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Europa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Nordamerika |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Tyskland) |
| **[Nelite](http://nelite.com/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asien |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Projektledarskap](http://www.projectleadership.net/azure)** | Nordamerika |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Europa |


## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"

