---
title: 'Anslutningsproviders och platser: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter anslutningsleverantör.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: pareshmu
ms.openlocfilehash: 6d1fa0235c91d17887a40bbe0be9831623fce775
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006978"
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

| **Geopolitisk region** | **Zon** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- | --- |
| **Myndigheter i Australien** | 1 | Centrala Australien, Centrala Australien 2 |Canberra, Canberra2 |
| **Europa** | 1 |Frankrike, centrala; Frankrike, södra; Europa, norra; Europa, västra; Storbritannien, västra; Storbritannien, södra |Amsterdam, Amsterdam2, Dublin, London, London2, Marseille, Newport(Wales), Paris, Zurich |
| **Nordamerika** | 1 |USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asien** | 2 |Östra Asien, Sydostasien |Hongkong, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Australien** | 2 |Sydöstra Australien, östra Australien |Melbourne, Perth, Sydney | 
| **Indien** | 2 |Västra Indien, centrala Indien, södra Indien |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | 2 |Västra Japan, östra Japan |Osaka, Tokyo |
| **Sydkorea** | 2 |Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **FÖRENADE ARABEMIRATEN** | 2 | Förenade Arabemiraten, centrala, Förenade Arabemiraten, Norra | Dubai |
| **Sydafrika** | 3 |Sydafrika, västra, Sydafrika, Norra |Kapstaden, Johannesburg |
| **Sydamerika** | 3 |Södra Brasilien |Sao Paulo |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **U.S. Government-moln** |US Gov, Arizona; US Gov, Iowa; US Gov, Texas; US Gov, Virginia; US DoD, centrala; US DoD, östra  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Östra Kina** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Norra Kina** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Centrala Tyskland, östra Tyskland |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="locations"></a>Platser för anslutningsleverantörer

I följande tabell visas platser efter tjänstleverantör. Om du vill visa tillgängliga leverantörer efter plats går du till [Tjänsteleverantörer efter plats](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Produktions-Azure

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365 och Dynamics 365** | **Platser** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | Stöds | Stöds | Chennai2, Mumbai2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Hongkong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Stöds |Stöds |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Stöds |Stöds |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Stöds |Stöds |Amsterdam, Hongkong SAR, London, Newport (Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **C3ntro** |Kommer snart |Kommer snart |Miami |
| **CDC** | Stöds | Stöds | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Stöds |Stöds |Las Vegas, New York, San Antonio, Silicon Valley, Tokyo, Toronto |
| **Chief Telecom** |Stöds |Stöds |Taipei |
| **China Telecom Global** |Stöds |Stöds inte |Hongkong SAR |
| **[Cologix](https://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Stöds |Stöds |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds |Amsterdam, Dublin, London, Paris, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Stöds |Stöds |Chicago, Denver, Los Angeles, New York, Silicon Valley, Washington DC, Washington DC2 |
| **DE-CIX** | Stöds |Stöds |Amsterdam2|
| **EIR** |Stöds |Stöds |Dublin|
| **Epsilon Global Communications** |Stöds |Stöds |Singapore, Singapore2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat Förenade Arabemiraten** |Stöds |Stöds |Dubai|
| **euNetworks** |Stöds |Stöds |Amsterdam, Dublin, London |
| **GÉANT** |Stöds |Stöds |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Stöds| Stöds | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Stöds |Stöds |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC |
| **Internet2** |Stöds |Stöds |Chicago, Dallas, Washington DC |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Stöds |Stöds |Osaka, Tokyo |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Stöds |Stöds |Kapstaden, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Stöds |Stöds |Amsterdam, Amsterdam2, Dublin, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Stöds |Stöds | Amsterdam, London2, Silicon Valley, Toronto |
| **Jisc** |Stöds |Stöds |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Stöds |Stöds |Seoul |
| **Kordia** | Stöds |Stöds |Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Stöds | Stöds | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Stöds |Stöds |Busan, Söul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds |Stöds |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Hongkong, Las Vegas, London, Los Angeles, Melbourne, Miami, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Stöds |Stöds |London |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Stöds |Stöds |Dallas, Los Angeles, Miami, Sao Paulo |
| **[Nästa generations data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Stöds |Stöds |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Stöds |Stöds |Melbourne, Perth, Sydney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Stöds |Stöds |Amsterdam, Hongkong, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT ÖSTRA](https://flets.com/cloudgateway/crossconnect/)** |Stöds |Stöds |Tokyo |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Stöds |Stöds |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Stöds |Stöds |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Stöds |Stöds |Amsterdam, Hongkong SAR, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Stöds |Stöds |Chicago, Hongkong, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Stöds |Stöds |Seoul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Stöds |Stöds |Chennai, Mumbai2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Stöds |Stöds |Singapore, Singapore2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Stöds |Stöds |Osaka, Tokyo |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Stöds |Stöds |Amsterdam, Chennai, Hongkong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **Telecity Group** |Stöds |Stöds |Amsterdam |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Stöds |Stöds |Amsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Stöds |Stöds |London |
| **Telenor** |Stöds |Stöds |Amsterdam, London |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Stöds | Stöds |Amsterdam, Chicago, Dallas, London, Washington DC |
| **Telmex Uninet**| Stöds | Stöds | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Stöds |Stöds |Melbourne, Singapore, Sydney |
| **[Telus](https://www.telus.com)** |Stöds |Stöds |Montreal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Stöds | Stöds | Kuala Lumpur |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Stöds |Stöds |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Hongkong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Stöds |Stöds inte |London, Singapore |
| **Vodafone Idea** | Stöds | Stöds | Mumbai, Mumbai2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC |

 **+** kommer snart

### <a name="national-cloud-environment"></a>Nationell molnmiljö

### <a name="us-government-cloud"></a>U.S. Government-moln

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Stöds |Stöds |New York, Phoenix |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds | Stöds | Chicago, Dallas, San Antonio, Seattle |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Stöds |Stöds |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Kina

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **China Telecom** |Stöds |Stöds inte |Beijing, Shanghai |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Stöds |Stöds inte |Beijing2, Shanghai2 |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Stöds |Stöds inte |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds inte |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Stöds |Stöds inte |Berlin |
| **Interxion** |Stöds |Stöds inte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds  | Stöds inte | Berlin |
| **T-Systems** |Stöds |Stöds inte |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Anslutningar via Exchange-leverantörer

Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="connectivity-through-additional-service-providers"></a>Anslutning med ytterligare leverantörer

| **Anslutningsleverantör** | **Exchange** | **Platser** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapore |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hongkong SAR |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponentiellt E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Mellanöstern brygga International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapore|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong SAR |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Kapstaden, Johannesburg |
| **[NexGen nätverk](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Inlägg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, London, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |
| **[Förenade Information Målwebbplatsens (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Anslutningar via datacenterleverantörer

| **Leverantör** | **Exchange** |
| --- | --- |
| **[Cyrus en](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Digitala verkl](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Datacenter](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenter](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

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

| **Systemintegratör** | **Kontinent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asien, Europa, Nordamerika, Sydamerika |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Asien
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Nordamerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Nordamerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Nordamerika |
| **[Ge IT-gruppen](https://itconsult.com.au/)** | Australien |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Tyskland) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[Ny signatur](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asien |
| **[Orange nätverk](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Nordamerika |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Sydamerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australien |

## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
