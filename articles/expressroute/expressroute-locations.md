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
   ms.date="09/20/2016"
   ms.author="cherylmc" />


# ExpressRoute-partners och peeringplatser

Tabellerna i den här artikeln innehåller information om ExpressRoute-anslutningsleverantörer, ExpressRoutes geografiska täckning, Microsoft-molntjänster som stöds via ExpressRoute och ExpressRoutes systemintegratörer (SI).

## <a name="partners"></a>ExpressRoute-anslutningsleverantörer

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
|**U.S. Government-moln**|Iowa (USA-förvaltad region), Virginia (USA-förvaltad region)|Chicago, Dallas, New York, Washington DC|
|**Kina**|Norra Kina, östra Kina|Beijing, Shanghai|
|**Tyskland**|Centrala Tyskland, östra Tyskland|Berlin+, Frankfurt|


Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.


## Anslutningsleverantörens platser

> [AZURE.SELECTOR]
[Platser efter leverantör](expressroute-locations.md#connectivity-provider-locations)
[Leverantörer efter plats](expressroute-locations-providers.md#connectivity-provider-locations)

### Produktions-Azure

| **Tjänstleverantör**  |**Microsoft Azure** | **Office 365 och CRM Online** | **Platser** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks]( http://www.aryaka.com/)** | Stöds | Stöds | Amsterdam, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Stöds | Stöds | Amsterdam, Chicago, Dallas, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Stöds | Stöds | Amsterdam, Hong Kong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
|**CenturyLink** | Kommer snart | Kommer snart| Silicon Valley |
|**China Telecom Global** | Stöds | Stöds inte | Hongkong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Stöds | Kommer snart | Dallas, Montreal+, Toronto |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Stöds | Stöds | Amsterdam, Dublin, London, Tokyo |
| **Comcast** | Stöds | Stöds | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Stöds | Stöds | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Stöds | Stöds | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, London, Los Angeles, Melbourne, New York, Osaka, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |  Stöds | Stöds | Amsterdam |
| **GÉANT** | Kommer snart | Kommer snart | Amsterdam+ |
| **[Internet Initiative Japan Inc. – IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Stöds | Stöds | Osaka, Tokyo |
| **[InterCloud]( https://www.intercloud.com/)** | Stöds | Stöds | Amsterdam, London, Singapore, Washington DC |
| **Internet Solutions – Cloud Connect** | Stöds | Stöds | Amsterdam, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Stöds | Stöds | Amsterdam, London, Paris |
| **Jisc** | Kommer snart | Kommer snart | London+ | 
| **[Level 3 Communications]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Stöds | Stöds | Amsterdam, Chicago, Dallas, Las Vegas+, London, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Stöds | Stöds | Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York, Seattle, Singapore, Sydney, Washington DC |
| **MTN** | Stöds | Stöds | London |
| **NEXTDC** | Stöds | Stöds | Melbourne, Sydney |
| **NTT Communications** | Stöds | Stöds | London, Los Angeles, Osaka, Tokyo |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Stöds | Stöds | Amsterdam, Hongkong, London, Silicon Valley, Singapore, Sydney, Washington DC |
| **PCCW Global Limited** | Stöds | Stöds | Hongkong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Stöds | Stöds | Singapore |
| **Softbank** | Stöds | Stöds | Osaka, Tokyo | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Stöds | Stöds | Amsterdam, Chennai, Hong Kong, London, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Stöds | Stöds | Amsterdam, Dublin, London |
| **Telefonica** | Stöds | Kommer snart | Sao Paulo |
| **Telenor** | Stöds | Stöds | Amsterdam, London |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Stöds | Kommer snart | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Stöds | Stöds | Amsterdam, Hong Kong, London, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **Vodafone** | Stöds | Stöds inte | London | 
| **[Zayo Group]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Stöds | Stöds | Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** kommer snart

### Nationella molnmiljöer

#### U.S. Government-moln

| **Tjänstleverantör**  |**Microsoft Azure** | **Office 365** | **Platser** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Stöds | Stöds | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Stöds | Stöds | Chicago, Dallas, New York, Washington DC |
| **[Level 3 Communications]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Stöds | Stöds | Chicago, New York+, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Stöds | Stöds | Chicago, Dallas+, New York, Washington DC |

#### Kina

| **Tjänstleverantör**  |**Microsoft Azure** | **Office 365** | **Platser** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Stöds | Stöds inte | Beijing, Shanghai|
Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

#### Tyskland

| **Tjänstleverantör**  |**Microsoft Azure** | **Office 365** | **Platser** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Stöds | Stöds inte | Berlin+, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Kommer snart | Stöds inte | Frankfurt+|
| **e-shelter** | Kommer snart | Stöds inte | Berlin+|
| **Interxion** | Stöds | Stöds inte | Frankfurt|

## <a name="nonpartners"></a>Anslutningar för leverantörer som inte finns i listan

Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

- Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
    - Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
- Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
    - Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

|**Anslutningsleverantör**|**Exchange**|**Platser**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapore|
|**Alaska Communications**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|New York, Washington DC|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## ExpressRoute-systemintegratörer

Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

|**Systemintegratör**|**Kontinent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asien, Europa, USA |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|USA|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asien |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | USA |
|**[Project Leadership](http://www.projectleadership.net/azure)** | USA |

## Nästa steg

- Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
- Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"



<!--HONumber=Sep16_HO3-->


