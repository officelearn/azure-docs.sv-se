---
title: 'Anslutningsleverantörer och platser: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter anslutningsleverantör.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: pareshmu
ms.openlocfilehash: 12cafc5888b46799780e066f7fd0de007670d6cc
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39481021"
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
| **Nordamerika** |USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Washington DC, Montreal, Quebec City, Toronto |
| **Sydamerika** |Södra Brasilien |Sao Paulo |
| **Europa** |Frankrike, centrala; Frankrike, södra; Europa, norra; Europa, västra; Storbritannien, västra; Storbritannien, södra |Amsterdam, Dublin, London, Newport (Wales), Paris |
| **Asien** |Östra Asien, Sydostasien |Hongkong SAR, Singapore, Singapore2 |
| **Japan** |Västra Japan, östra Japan |Osaka, Tokyo |
| **Australien** |Sydöstra Australien, östra Australien |Melbourne, Sydney |
| **Australiensiska myndigheter** | Centrala Australien, Centrala Australien 2 |Canberra, Canberra2 | 
| **Indien** |Västra Indien, centrala Indien, södra Indien |Chennai, Mumbai |
| **Sydkorea** |Sydkorea, centrala; Sydkorea, södra |Busan, Söul |
| **Sydafrika** |[Sydafrika, västra+, Sydafrika, norra+](https://blogs.microsoft.com/blog/2017/05/18/microsoft-deliver-microsoft-cloud-datacenters-africa/) |Kapstaden, Johannesburg |

 **+** kommer snart

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; US Gov, Iowa; US Gov, Texas; US Gov, Virginia; US DoD, centrala; US DoD, östra  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="locations"></a>Platser för anslutningsleverantörer

I följande tabell visas platser efter tjänstleverantör. Om du vill visa tillgängliga leverantörer efter plats går du till [Tjänsteleverantörer efter plats](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Produktions-Azure
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365 och Dynamics 365** | **Platser** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Airtel](http://www.airtel.in/creatingsmiles/)** | Stöds | Stöds | Chennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Stöds |Stöds |Amsterdam, Dallas, Hongkong, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Stöds |Stöds |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Stöds |Stöds |Montreal, Toronto, Quebec City |
| **[British Telecom](https://globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Stöds |Stöds |Amsterdam, Hong Kong SAR, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://c3ntro.com/)** |Kommer snart |Kommer snart |Miami |
| **CDC** | Stöds | Stöds | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Stöds |Stöds |Las Vegas, New York, San Antonio, Silicon Valley, Tokyo, Toronto |
| **China Telecom Global** |Stöds |Stöds inte |Hongkong SAR |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Stöds |Stöds |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds |Amsterdam, Dublin, London, Paris, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Stöds |Stöds |Chicago, Denver, Los Angeles, New York, Silicon Valley, Washinton DC |
| **eir** |Stöds |Stöds |Dublin|
| **Epsilon Global Communications** |Stöds |Stöds |Singapore |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong, London, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Stöds |Stöds |Amsterdam, London |
| **GÉANT** |Stöds |Stöds |Amsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Stöds| Stöds | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Stöds |Stöds |Amsterdam, London, Paris, Singapore, Washington DC |
| **Internet2** |Stöds |Stöds |Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Stöds |Stöds |Osaka, Tokyo |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Stöds |Stöds |Kapstaden, Johannesburg, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Stöds |Stöds |Amsterdam, Dublin, London, Paris |
| **[IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)**|Stöds |Stöds | Silicon Valley, Toronto |
| **Jisc** |Stöds |Stöds |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Stöds |Stöds |Seoul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Stöds | Stöds | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Newport, Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Stöds |Stöds |Busan, Söul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Hong Kong, Las Vegas, London, Los Angeles, Melbourne, Miami, New York, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **MTN** |Stöds |Stöds |London |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Stöds |Stöds |Dallas, Miami, Sao Paulo |
| **[Nästa datageneration](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Stöds |Stöds |Newport(Wales) |
| **NEXTDC** |Stöds |Stöds |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Stöds |Stöds |Amsterdam, Hongkong, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Stöds |Stöds |Tokyo |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Stöds |Stöds |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Stöds |Stöds |Melbourne+ , Sydney |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Stöds |Stöds |Amsterdam, Hongkong SAR, London, Paris+, Silicon Valley, Singapore, Sydney, Washington DC |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **PCCW Global Limited** |Stöds |Stöds |Hongkong SAR |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Stöds |Stöds |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Stöds |Stöds |Chennai, Mumbai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Stöds |Stöds |Singapore, Singapore2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Stöds |Stöds |Osaka, Tokyo |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Stöds |Stöds |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Stöds |Stöds |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Stöds |Stöds |London |
| **Telenor** |Stöds |Stöds |Amsterdam, London |
| **[Telia-operatör](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Stöds | Stöds |London, Washington DC |
| **Telmex Uninet**| Kommer snart | Kommer snart | Dallas+ |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Telus](http://www.telus.com)** |Stöds |Stöds |Montreal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Stöds |Stöds |Sao Paulo |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Hongkong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Stöds |Stöds inte |London |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Los Angeles, Montreal, New York, Silicon Valley, Toronto, Washington DC |

 **+** kommer snart

### <a name="national-cloud-environment"></a>Nationell molnmiljö

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Chicago, Washington DC |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Stöds |Stöds |Phoenix, New York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds | Stöds | Chicago, Dallas, San Antonio |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Stöds |Stöds |Chicago, Dallas, New York, Washington DC |

### <a name="china"></a>Kina
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **China Telecom** |Stöds |Stöds inte |Beijing, Shanghai |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland
| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds inte |Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds inte |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Stöds |Stöds inte |Berlin |
| **Interxion** |Stöds |Stöds inte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds  | Stöds inte | Berlin |
| **T-Systems** |Stöds |Stöds inte |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Anslutningar via Exchange-leverantörer

Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="connectivity-through-additional-service-providers"></a>Anslutning med ytterligare leverantörer

| **Anslutningsleverantör** | **Exchange** | **Platser** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapore |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](http://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Franfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponentiellt E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Washington DC |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapore|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, London, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |London|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Anslutningar via datacenterleverantörer
| **Leverantör** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenters](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Anslutning via NREN (National Research and Education Networks)

| **Leverantör**|
| --- |
| **AARNET**| 
| **DeIC, via GÉANT**|
| **GARR, via GÉANT**|
| **GÉANT**|
| **HEAnet, via GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, via GÉANT**|
| **SINET**|
| **Surfnet, via GÉANT**|

* Om din anslutningsleverantör inte finns med här kan du se om den är ansluten till någon av de ExpressRoute Exchange-partner som anges ovan.

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Systemintegrator** | **Kontinent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Asien, Europa, Nordamerika, Sydamerika |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](http://www.ensyst.com.au)** | Asien
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Nordamerika |
| **[FlexManage](http://www.flexmanage.com/cloud)** | Nordamerika |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Europa |
| **[Lightstream](https://www.ltstream.com/expressroute)** | Nordamerika |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Australien |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Tyskland) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/connectivity.html)** | Asien |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Nordamerika |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australien |


## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
