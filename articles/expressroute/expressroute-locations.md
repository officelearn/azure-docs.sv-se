---
title: ExpressRoute-platser | Microsoft Docs
description: "Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b2d7ba14c7cbc5c082a5f962559936e1a60827f2
ms.openlocfilehash: fd018ed25e5c309a85bb2078d00f2bb0b504e5d2


---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser
Tabellerna i den här artikeln innehåller information om ExpressRoute-anslutningsleverantörer, ExpressRoutes geografiska täckning, Microsoft-molntjänster som stöds via ExpressRoute och ExpressRoutes systemintegratörer (SI).

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Anslutningsproviders för ExpressRoute
ExpressRoute stöds i alla Azures regioner och platser. Följande karta innehåller en lista med Azures regioner och ExpressRoute-platser. ExpressRoute-platser avser platser där Microsoft peerkopplas med flera tjänstleverantörer.

![Platskarta][0]

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Azure-regioner till ExpressRoute-platser inom en geopolitisk region.
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Nordamerika** |Östra USA, västra USA, östra USA 2, centrala USA, södra centrala USA, norra centrala USA, centrala Kanada, östra Kanada |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto |
| **Sydamerika** |Södra Brasilien |Sao Paulo |
| **Europa** |Norra Europa, Västra Europa, Västra Storbritannien, Södra Storbritannien |Amsterdam, Dublin, London, Newport (Wales), Paris |
| **Asien** |Östra Asien, Sydostasien |Hongkong, Singapore |
| **Japan** |Västra Japan, östra Japan |Osaka, Tokyo |
| **Australien** |Sydöstra Australien, östra Australien |Melbourne, Sydney |
| **Indien** |Västra Indien, centrala Indien, södra Indien |Chennai, Mumbai |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- | --- |
| **Moln för amerikanska myndigheter** |Iowa (USA-förvaltad region), Virginia (USA-förvaltad region) |Chicago, Dallas, New York, Washington DC |
| **Kina** |Norra Kina, östra Kina |Beijing, Shanghai |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>Platser för anslutningsleverantörer
> [!div class="op_single_selector"]
> * [Platser efter leverantör](expressroute-locations.md#locations)
> * [Leverantörer efter plats](expressroute-locations-providers.md#locations)
>
>

### <a name="production-azure"></a>Produktions-Azure
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365 och CRM Online** | **Platser** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Aryaka Networks](http://www.aryaka.com/)** |Stöds |Stöds |Amsterdam, Dallas, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Stöds |Stöds |Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Stöds |Stöds |Amsterdam, Hong Kong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Kommer snart |Kommer snart |Silicon Valley |
| **China Telecom Global** |Stöds |Stöds inte |Hongkong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Stöds |Stöds |Dallas, Montreal+, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds |Amsterdam, Dublin, London, Tokyo |
| **Comcast** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **Konsol**| Stöds | Stöds |Silicon Valley |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Stöds |Stöds |Los Angeles |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, Osaka, Paris+, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Stöds |Stöds |Amsterdam |
| **GÉANT** |Stöds |Stöds |Amsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Stöds |Stöds |Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** |Stöds |Stöds |Amsterdam, London, Singapore, Washington DC |
| **Internet Solutions - Cloud Connect** |Stöds |Stöds |Amsterdam, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |Stöds |Stöds |Amsterdam, London, Paris |
| **Jisc** |Stöds |Stöds |London |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Las Vegas+, London, Seattle, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds |Stöds |Dallas, Hongkong, Las Vegas, Los Angeles, Melbourne, New York, Seattle, Singapore, Sydney, Toronto, Washington DC |
| **MTN** |Stöds |Stöds |London |
| **[Nästa datageneration](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Stöds |Stöds |Newport(Wales) |
| **NEXTDC** |Stöds |Stöds |Melbourne, Sydney |
| **NTT Communications** |Stöds |Stöds |London, Los Angeles, Osaka, Singapore, Tokyo, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Stöds |Stöds |Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **PCCW Global Limited** |Stöds |Stöds |Hongkong |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Stöds |Stöds |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Stöds |Stöds |Singapore |
| **Softbank** |Stöds |Stöds |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Stöds |Stöds |Amsterdam, Chennai, Hong Kong, London, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Stöds |Stöds |Amsterdam, Dublin, London |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Stöds |Stöds |Sao Paulo |
| **Telenor** |Stöds |Stöds |Amsterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Stöds |Stöds |Amsterdam, Hong Kong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Stöds |Stöds inte |London |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Stöds |Stöds |Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** kommer snart

### <a name="national-cloud-environment"></a>Nationell molnmiljö

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Chicago, Dallas, New York, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Chicago, New York+, Washington DC |
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
| **e-shelter** |Stöds |Stöds inte |Berlin |
| **Interxion** |Stöds |Stöds inte |Frankfurt |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Anslutning för leverantörer som inte listas
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
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
| **[Arteria-Net](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokyo |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Exponentiellt E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |New York, Washington DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Silicon Valley, Washington DC |
| **[XO Communications](http://www.xo.com/)** |Equinix |Silicon Valley |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |


## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Systemintegrator** | **Kontinent** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** |Asien, Europa, USA |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** |Europa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** |USA |
| **[Nelite](http://nelite.com/)** |Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** |Asien |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** |USA |
| **[Projektledarskap](http://www.projectleadership.net/azure)** |USA |


## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"



<!--HONumber=Dec16_HO3-->


