---
title: Azures över-nätverks anslutning
description: På den här sidan beskrivs ett program scenario för anslutning mellan nätverk och lösning baserad på funktioner i Azure-nätverk.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644264"
---
# <a name="cross-network-connectivity"></a>Anslutning mellan nätverk

Fabrikam Inc. har en stor fysisk närvaro och Azure-distribution i USA, östra. Fabrikam har Server dels anslutningar mellan lokala och Azure-distributioner via ExpressRoute. På samma sätt har contoso Ltd. en närvaro och Azure-distribution i USA, västra. Contoso har Server dels anslutningar mellan lokala och Azure-distributioner via ExpressRoute.  

Fabrikam Inc. köper contoso Ltd. Efter sammanslagningen vill Fabrikam ansluta nätverken till varandra. Följande bild illustrerar scenariot:

 [![1]][1]

De streckade pilarna i mitten av figuren ovan visar önskade nätverks anslutningar. Mer specifikt finns det tre typer av korslänkade anslutningar: 1) Fabrikam och contoso virtuella nätverk Cross Connect, 2) korsande regionala lokala och virtuella nätverk kors anslutningar (det vill säga ansluter Fabrikam lokalt nätverk till contoso VNet och ansluta contoso lokalt nätverk till Fabrikam VNet) och 3) Fabrikam och contoso on-premises Network-Interconnect. 

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd, före fusionen.

[![2]][2]

I följande tabell visas effektiva vägar för en virtuell dator i Contoso-prenumerationen, före fusionen. Per tabell är den virtuella datorn i VNet medveten om VNet-adressutrymmet och det lokala nätverket Contoso, förutom standardvärdena. 

[![4]][4]

I följande tabell visas routningstabellen för den privata peering-ExpressRoute av Fabrikam Inc. före fusionen.

[![3]][3]

I följande tabell visas effektiva vägar för en virtuell dator i den Fabrikam-prenumerationen innan fusionen. Per tabell är den virtuella datorn i VNet medveten om det virtuella nätverkets adress utrymme och det lokala nätverket Fabrikam, förutom standardvärdena.

[![5]][5]

I den här artikeln går vi igenom steg för steg och diskuterar hur du uppnår önskade kors anslutningar med följande funktioner i Azure-nätverket:

* [Virtuell nätverkspeering][Virtual network peering] 
* [ExpressRoute anslutning för virtuellt nätverk][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Korsa anslutning virtuella nätverk

Det virtuella nätverkets peering (VNet-peering) ger den optimala och bästa nätverks prestanda vid anslutning av två virtuella nätverk. VNet-peering stöder peering av två virtuella nätverk i samma Azure-region (kallas vanligt vis VNet-peering) och i två olika Azure-regioner (vanligt vis kallade global VNet-peering). 

Vi konfigurerar global VNet-peering mellan virtuella nätverk i Contoso och Fabrikam Azure-prenumerationer. Information om hur du skapar peering för virtuella nätverk mellan två virtuella nätverk finns i artikeln [skapa en peering för virtuella nätverk][Configure VNet peering] .

Följande bild visar nätverks arkitekturen när du har konfigurerat global VNet-peering.

[![3-6]][6]

I följande tabell visas de vägar som är kända för den virtuella Contoso-prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av att koppla samman de virtuella nätverken.

[![3,7]][7]

I följande tabell visas de vägar som är kända för den virtuella fabriks prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av att koppla samman de virtuella nätverken.

[![7,8]][8]

VNet-peering länkar direkt två virtuella nätverk (se det finns inget nästa hopp för *VNetGlobalPeering* -posten i ovanstående två tabeller)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Korsa anslutning av virtuella nätverk till lokala nätverk

Vi kan ansluta en ExpressRoute-krets till flera virtuella nätverk. Se [prenumerations-och tjänst begränsningar][Subscription limits] för maximalt antal virtuella nätverk som kan anslutas till en ExpressRoute-krets. 

Nu ska vi ansluta Fabrikam ExpressRoute-kretsen till contoso-prenumerationens VNet och på samma sätt contoso ExpressRoute-krets till Fabrikam-prenumerationens VNet för att möjliggöra anslutning mellan virtuella nätverk och lokala nätverk. För att ansluta ett virtuellt nätverk till en ExpressRoute-krets i en annan prenumeration måste vi skapa och använda en auktorisering.  Se artikeln: [ansluta ett virtuellt nätverk till en ExpressRoute-krets][Connect-ER-VNet].

Följande bild visar nätverks arkitekturen när du har konfigurerat ExpressRoute-Cross-anslutningen till de virtuella nätverken.

[![1.9]][9]

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd., efter att ha kopplat virtuella nätverk över till lokala nätverk via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

[![10]][10]

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Fabrikam Inc. efter att ha kopplat samman virtuella nätverk till de lokala nätverken via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

[![11.3]][11]

I följande tabell visas de vägar som är kända för den virtuella Contoso-prenumerationen. Var noga med de *virtuella nätverksgateway* för tabellen. Den virtuella datorn ser vägar för både lokala nätverk.

[![12.5]][12]

I följande tabell visas de vägar som är kända för den virtuella fabriks prenumerationen. Var noga med de *virtuella nätverksgateway* för tabellen. Den virtuella datorn ser vägar för både lokala nätverk.

[![13.4]][13]

>[!NOTE]
>I antingen Fabrikam-och/eller Contoso-prenumerationer kan du också ha eker-virtuella nätverk till respektive hubb-VNet (en nav och eker-design illustreras inte i arkitektur diagrammen i den här artikeln). Kors anslutningarna mellan Hub VNet-gatewayerna till ExpressRoute kommer också att tillåta kommunikation mellan öst-och västra hubbar och ekrar.
>

## <a name="cross-connecting-on-premises-networks"></a>Korsa anslutning av lokala nätverk

ExpressRoute Global Reach ger anslutning mellan lokala nätverk som är anslutna till olika ExpressRoute-kretsar. Vi konfigurerar Global Reach mellan contoso-och Fabrikam ExpressRoute-kretsar. Eftersom ExpressRoute-kretsarna finns i olika prenumerationer måste vi skapa och använda en auktorisering. Mer information om steg för steg finns i [Konfigurera ExpressRoute Global Reach][Configure Global Reach] artikel.

Följande bild visar nätverks arkitekturen när du har konfigurerat Global Reach.

[![längre]][14]

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd. efter att du har konfigurerat Global Reach. Se att routningstabellen har vägar som hör till både det lokala nätverket. 

[![15.4]][15]

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Fabrikam Inc. När du har konfigurerat Global Reach. Se att routningstabellen har vägar som hör till både det lokala nätverket.

[![16]][16]

## <a name="next-steps"></a>Nästa steg

Se [vanliga frågor och svar om virtuella nätverk][VNet-FAQ]för ytterligare frågor om VNet och VNET-peering. Se [vanliga][ER-FAQ] frågor och svar om ExpressRoute för ytterligare frågor om ExpressRoute och virtuell nätverks anslutning.

Global Reach distribueras på ett land/en region efter land/region. Se [ExpressRoute Global Reach][Global Reach]för att se om Global Reach är tillgänglig i de länder/regioner som du vill ha.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "program scenariot"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "contoso ExpressRoute Route-tabellen före fusionen"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute route-tabell före fusion"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "contoso VM-vägar före fusion"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM-vägar före fusion"
[6]: ./media/cross-network-connectivity/vnet-peering.png "arkitekturen efter VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "contoso VM-vägar efter VNet-peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam virtuella dator vägar efter VNet-peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "arkitekturen efter ExpressRoute-kors anslutning"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "contoso ExpressRoute route-tabell efter kors anslutning av ExR och virtuella nätverk"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute Route Table efter kors anslutning av ExR och virtuella nätverk"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "contoso VM-vägar efter mellan koppling av ExR och virtuella nätverk"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam virtuella dator vägar efter anslutning av ExR och virtuella nätverk"
[14]: ./media/cross-network-connectivity/globalreach.png "arkitekturen när du har konfigurerat Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "contoso ExpressRoute route-tabell efter Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute väg tabell efter Global Reach"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq