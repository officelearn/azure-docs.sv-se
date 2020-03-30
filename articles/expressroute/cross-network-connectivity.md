---
title: Azure-anslutning mellan nätverk
description: Den här sidan beskriver ett programscenario för anslutning mellan nätverk och lösning baserat på Azure-nätverksfunktioner.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644264"
---
# <a name="cross-network-connectivity"></a>Anslutning mellan nätverk

Fabrikam Inc. har en stor fysisk närvaro och Azure-distribution i östra USA. Fabrikam har backend-anslutning mellan sina lokala och Azure-distributioner via ExpressRoute. På samma sätt har Contoso Ltd. en närvaro- och Azure-distribution i västra USA. Contoso har backend-anslutning mellan sina lokala och Azure-distributioner via ExpressRoute.  

Fabrikam Inc. förvärvar Contoso Ltd. Efter sammanslagningen vill Fabrikam koppla samman nätverken. Följande bild illustrerar scenariot:

 [![1]][1.]

De streckade pilarna i mitten av ovanstående bild visar önskade nätverkssammanlänkningar. Närmare bestämt finns det tre typer av korsanslutningar som önskas: 1) Fabrikam och Contoso VNets cross connect, 2) Cross regional lokalt och virtuella nätverk korsanslutningar (det vill vara att ansluta Fabrikam lokalt nätverk till Contoso VNet och ansluta Contoso lokalt nätverk till Fabrikam VNet) och 3) Fabrikam och Contoso lokalt nätverk korsanslutning. 

I följande tabell visas rutttabellen för den privata peeringen för ExpressRoute of Contoso Ltd., före sammanslagningen.

[![2]][2]

I följande tabell visas de effektiva vägarna för en virtuell dator i Contoso-prenumerationen före sammanslagningen. Enligt tabellen är den virtuella datorn på det virtuella nätverket medveten om VNet-adressutrymmet och det lokala contoso-nätverket, förutom standardnätverket. 

[![4]][4.]

I följande tabell visas flödestabellen för den privata peeringen för ExpressRoute of Fabrikam Inc., före sammanslagningen.

[![3]][3.]

I följande tabell visas de effektiva vägarna för en virtuell dator i Fabrikam-prenumerationen före sammanslagningen. Enligt tabellen är den virtuella datorn på det virtuella nätverket medveten om VNet-adressutrymmet och det lokala Fabrikam-nätverket, förutom standardnätverket.

[![5]][5]

I den här artikeln ska vi gå igenom steg för steg och diskutera hur du uppnår önskade korsanslutningar med hjälp av följande Azure-nätverksfunktioner:

* [Virtuell nätverks peering][Virtual network peering] 
* [ExpressRoute-anslutning för virtuellt nätverk][connection]
* [Global räckvidd][Global Reach] 

## <a name="cross-connecting-vnets"></a>Korsanslutning av virtuella nätverk

Virtuell nätverks peering (VNet peering) ger den mest optimala och bästa nätverksprestanda när du ansluter två virtuella nätverk. VNet-peering stöder peering två virtuella nätverk både inom samma Azure-region (vanligen kallad VNet-peering) och i två olika Azure-regioner (kallas ofta Global VNet-peering). 

Nu ska vi konfigurera Global VNet-peering mellan virtuella nätverk i Contoso- och Fabrikam Azure-prenumerationer. Hur du skapar den virtuella nätverks peering mellan två virtuella nätverk, se [Skapa ett virtuellt nätverk peering][Configure VNet peering] artikel.

Följande bild visar nätverksarkitekturen efter att ha konfigurerat Global VNet-peering.

[![6]][6]

I följande tabell visas de vägar som är kända för den virtuella datorn för Contoso-prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av korsanslutning av virtuella nätverk.

[![7]][7]

I följande tabell visas de vägar som är kända för den virtuella datorn för Fabrikam-prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av korsanslutning av virtuella nätverk.

[![8]][8]

VNet peering länkar direkt två virtuella nätverk (se det finns ingen nästa hop för *VNetGlobalPeering* post i ovanstående två tabeller)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Korsanslutning av virtuella nätverk till lokala nätverk

Vi kan ansluta en ExpressRoute-krets till flera virtuella nätverk. Se [Prenumerations- och tjänstgränser][Subscription limits] för det maximala antalet virtuella nätverk som kan anslutas till en ExpressRoute-krets. 

Låt oss ansluta Fabrikam ExpressRoute-kretsen till Contoso-prenumerationenVNet och på samma sätt Contoso ExpressRoute-kretsen till Fabrikam-prenumerationenS virtuella nätverk för att möjliggöra korsanslutning mellan virtuella nätverk och lokala nätverk. Om du vill ansluta ett virtuellt nätverk till en ExpressRoute-krets i en annan prenumeration måste vi skapa och använda en auktorisering.  Se artikeln: [Anslut ett virtuellt nätverk till en ExpressRoute-krets][Connect-ER-VNet].

Följande bild visar nätverksarkitekturen efter att ha konfigurerat ExpressRoute-korsanslutningen till de virtuella nätverken.

[![9]][9]

I följande tabell visas flödestabellen för den privata peeringen för ExpressRoute of Contoso Ltd., efter att de virtuella nätverken korskopplats till de lokala nätverken via ExpressRoute. Se till att vägtabellen har vägar som tillhör båda de virtuella nätverken.

[![10]][10]

I följande tabell visas flödestabellen för den privata peeringen för ExpressRoute of Fabrikam Inc., efter att de virtuella nätverken korskopplats till de lokala nätverken via ExpressRoute. Se till att vägtabellen har vägar som tillhör båda de virtuella nätverken.

[![11]][11]

I följande tabell visas de vägar som är kända för den virtuella datorn för Contoso-prenumerationen. Var uppmärksam på *virtuella nätverksgatewayposter* i tabellen. Den virtuella datorn ser vägar för båda de lokala nätverken.

[![12]][12]

I följande tabell visas de vägar som är kända för den virtuella datorn för Fabrikam-prenumerationen. Var uppmärksam på *virtuella nätverksgatewayposter* i tabellen. Den virtuella datorn ser vägar för båda de lokala nätverken.

[![13]][13]

>[!NOTE]
>I antingen Fabrikam- och/eller Contoso-prenumerationerna kan du också ha eker-virtuella nätverk till respektive nav-VNet (en hubb- och ekerdesign illustreras inte i arkitekturdiagrammen i den här artikeln). De korsanslutningar mellan hubvnet-gateways till ExpressRoute kommer också att möjliggöra kommunikation mellan öst och väst nav och ekrar.
>

## <a name="cross-connecting-on-premises-networks"></a>Korsanslutning av lokala nätverk

ExpressRoute Global Reach tillhandahåller anslutning mellan lokala nätverk som är anslutna till olika ExpressRoute-kretsar. Nu ska vi konfigurera Global Reach mellan Contoso- och Fabrikam ExpressRoute-kretsar. Eftersom ExpressRoute-kretsarna finns i olika prenumerationer måste vi skapa och använda en auktorisering. Se [Konfigurera artikel i ExpressRoute Global Reach][Configure Global Reach] steg för steg.

Följande bild visar nätverksarkitekturen när du har konfigurerat Global Reach.

[![14]][14]

I följande tabell visas rutttabellen för den privata peeringen för ExpressRoute of Contoso Ltd., efter att ha konfigurerat Global Reach. Se till att rutttabellen har rutter som tillhör båda de lokala nätverken. 

[![15]][15]

I följande tabell visas rutttabellen för den privata peeringen för ExpressRoute av Fabrikam Inc., efter att ha konfigurerat Global Reach. Se till att rutttabellen har rutter som tillhör båda de lokala nätverken.

[![16]][16]

## <a name="next-steps"></a>Nästa steg

Se [vanliga frågor om virtuella nätverk][VNet-FAQ]för ytterligare frågor om VNet och VNet-peering. Se Vanliga frågor och svar om [ExpressRoute][ER-FAQ] för ytterligare frågor om ExpressRoute och virtuell nätverksanslutning.

Global Reach lanseras på land/region efter land/region. Information om du vill se om Global Reach är tillgängligt i de länder/regioner som du vill använda finns i [ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scenariot för applikation"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute rutttabell före fusion"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute rutttabell före sammanslagning"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM-rutter före sammanslagning"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM-rutter före sammanslagning"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Arkitekturen efter VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM-vägar efter VNet-peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM-vägar efter VNet-peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Arkitekturen efter ExpressRoutes arg anslutning"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute rutttabell efter korsanslutning ExR och virtuella nätverk"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute rutttabell efter korsanslutning ExR och virtuella nätverk"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso VM-vägar efter korsanslutning av ExR och virtuella nätverk"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam VM-vägar efter korsanslutning av ExR och virtuella nätverk"
[14]: ./media/cross-network-connectivity/globalreach.png "Arkitekturen efter att ha konfigurerat Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute rutttabell efter Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute rutttabell efter Global Reach"

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