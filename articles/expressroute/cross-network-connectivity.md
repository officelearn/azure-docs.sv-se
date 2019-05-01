---
title: Azure cross-nätverksanslutning | Microsoft Docs
description: Den här sidan beskriver ett scenario för programmet för nätverksanslutningar och lösning som baseras på Azure nätverksfunktioner.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64865994"
---
# <a name="cross-network-connectivity"></a>Anslutning mellan nätverk

Fabrikam, Inc. har ett stort fysisk närvaro och Azure-distribution i USA, östra. Fabrikam har en backend-anslutning mellan lokala och Azure-distributioner via ExpressRoute. På samma sätt kan har Contoso Ltd. en närvaro och Azure-distribution i västra USA. Contoso har backend-anslutning mellan sina lokala och Azure-distributioner via ExpressRoute.  

Fabrikam Inc. acquires Contoso Ltd. Följande fusion vill Fabrikam sammankoppling nätverken. Följande bild visar scenario:

 [![1]][1]

Streckad pilar mitt i bilden ovan visar anslutningarna för önskade nätverket. Mer specifikt finns tre typer mellan anslutningar önskade: (1) Fabrikam och Contoso VNets mellan ansluta, 2) mellan regionala lokala och virtuella nätverk mellan ansluter (som, ansluter Fabrikam lokala nätverk till Contoso VNet och ansluter Contoso lokala nätverk till Fabrikam VNet), och (3) Fabrikam och Contoso Anslut lokala nätverk för olika plattformar. 

I följande tabell visas i routningstabellen för privat peering i ExpressRoute för Contoso Ltd., innan sammanslagningen.

[![2]][2]

I följande tabell visar de effektiva vägarna för en virtuell dator i Contoso-prenumerationen innan sammanslagningen. Per tabell har den virtuella datorn på det virtuella nätverket om virtuella nätverkets adressutrymme och Contoso, lokalt nätverk, förutom dem som standard. 

[![4]][4]

I följande tabell visas i routningstabellen för privat peering i ExpressRoute för Fabrikam Inc., innan sammanslagningen.

[![3]][3]

I följande tabell visar de effektiva vägarna för en virtuell dator i Fabrikam-prenumerationen innan sammanslagningen. Per tabell har den virtuella datorn på det virtuella nätverket om virtuella nätverkets adressutrymme och Fabrikam, lokalt nätverk, förutom dem som standard.

[![5]][5]

Vi ska gå igenom steg för steg i den här artikeln och diskutera hur du uppnår önskat mellan anslutningar som använder följande funktioner i Azure-nätverk:

* [Virtuell nätverkspeering][Virtual network peering] 
* [Virtuellt nätverk ExpressRoute-anslutning][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Mellan ansluta virtuella nätverk

Virtuell nätverkspeering (VNet-peering) ger de bästa och bästa nätverksprestanda när du ansluter två virtuella nätverk. VNet-peering har stöd för peering två virtuella nätverk både inom samma Azure-region (vanligtvis kallad VNet-peering) och i två olika Azure-regioner (vanligtvis kallad Global VNet-peering). 

Nu ska vi konfigurera Global VNet-peering mellan de virtuella nätverken i Contoso och Fabrikam Azure-prenumerationer. Om hur du skapar det virtuella nätverkets peering mellan två virtuella nätverk finns i [skapa en virtuell nätverkspeering] [ Configure VNet peering] artikeln.

Följande bild illustrerar nätverksarkitekturen när du har konfigurerat Global VNet-peering.

[![6]][6]

I följande tabell visas vägarna kända till Contoso-prenumeration VM. Ta hänsyn till den sista posten i tabellen. Den här posten är resultatet av mellan ansluter virtuella nätverk.

[![7]][7]

I följande tabell visas vägarna kända för Fabrikam-prenumerationen VM. Ta hänsyn till den sista posten i tabellen. Den här posten är resultatet av mellan ansluter virtuella nätverk.

[![8]][8]

Direkt VNet-peering länkar två virtuella nätverk (se det finns inga nästa hopp för *VNetGlobalPeering* post i ovanstående två tabeller)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Mellan ansluta virtuella nätverk till lokala nätverk

Vi kan ansluta en ExpressRoute-krets till flera virtuella nätverk. Se [prenumeration och tjänstbegränsningar] [ Subscription limits] för det maximala antalet virtuella nätverk som kan anslutas till en ExpressRoute-krets. 

Nu ska vi ansluta Fabrikam ExpressRoute-krets till Contoso prenumeration VNet och på samma sätt Contoso ExpressRoute-krets till Fabrikam prenumeration VNet genom att aktivera mellan anslutning mellan virtuella nätverk och lokala nätverk. För att ansluta ett virtuellt nätverk till en ExpressRoute-krets i en annan prenumeration, måste vi skapa och använda en auktorisering.  Finns i artikeln: [Ansluta ett virtuellt nätverk till en ExpressRoute-krets][Connect-ER-VNet].

Följande bild illustrerar nätverksarkitekturen med när du har konfigurerat ExpressRoute över anslutning till de virtuella nätverken.

[![9]][9]

I följande tabell visas i routningstabellen för privat peering på den ExpressRoute av Contoso Ltd., efter mellan virtuella nätverk att ansluta till lokala nätverk via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

[![10]][10]

I följande tabell visas i routningstabellen för privat peering för den ExpressRoute för Fabrikam Inc., efter mellan virtuella nätverk att ansluta till lokala nätverk via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

[![11]][11]

I följande tabell visas vägarna kända till Contoso-prenumeration VM. Uppmärksam på *virtuell nätverksgateway* poster i tabellen. Den virtuella datorn ser vägar för både de lokala nätverk.

[![12]][12]

I följande tabell visas vägarna kända för Fabrikam-prenumerationen VM. Uppmärksam på *virtuell nätverksgateway* poster i tabellen. Den virtuella datorn ser vägar för både de lokala nätverk.

[![13]][13]

>[!NOTE]
>Antingen i ekernätverk Fabrikam och/eller Contoso-prenumerationer som du kan också ha virtuella till respektive hubbens virtuella nätverk (en nav och ekrar design inte visas i diagrammen arkitekturen i den här artikeln). Mellan anslutningarna mellan hub-gatewayer för virtuellt nätverk till ExpressRoute kommer också att tillåta kommunikation mellan Öst och Väst NAV och ekrar.
>

## <a name="cross-connecting-on-premises-networks"></a>Mellan ansluta lokala nätverk

ExpressRoute Global räckvidd tillhandahåller anslutningen mellan lokala nätverk som är anslutna till olika ExpressRoute-kretsar. Nu ska vi konfigurera Global räckvidd mellan Contoso och Fabrikam ExpressRoute-kretsar. Eftersom ExpressRoute-kretsar finns i olika prenumerationer, måste vi skapa och använda en auktorisering. Se [konfigurera ExpressRoute Global räckvidd] [ Configure Global Reach] artikel för stegvis vägledning.

Följande bild illustrerar nätverksarkitekturen när du har konfigurerat Global räckvidd.

[![14]][14]

I följande tabell visas i routningstabellen för privat peering på den ExpressRoute av Contoso Ltd., när du har konfigurerat Global räckvidd. Se att routningstabellen har vägar som tillhör både de lokala nätverk. 

[![15]][15]

I följande tabell visas i routningstabellen för för privat peering i ExpressRoute för Fabrikam Inc., när du har konfigurerat Global räckvidd. Se att routningstabellen har vägar som tillhör både de lokala nätverk.

[![16]][16]

## <a name="next-steps"></a>Nästa steg

Se [virtuellt nätverk vanliga frågor och svar][VNet-FAQ]för eventuella ytterligare frågor på VNet och VNet-peering. Se [ExpressRoute vanliga frågor och svar] [ ER-FAQ] för ytterligare frågor på ExpressRoute och virtuella nätverksanslutningar.

Global räckvidd distribueras på basis av land/region av land/region. Om du vill se om Global räckvidd är tillgängligt i länder/regioner som du vill se [ExpressRoute Global räckvidd][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "programmet scenario"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute routningstabellen innan sammanslagning"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute routningstabellen innan sammanslagning"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM dirigerar innan sammanslagning"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM dirigerar innan sammanslagning"
[6]: ./media/cross-network-connectivity/vnet-peering.png "arkitekturen efter VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM dirigerar efter VNet-peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM dirigerar efter VNet-peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "the arkitektur när expressroute-anslutningar mellan anslutning"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute routningstabellen efter anslutande ExR och virtuella nätverk"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute routningstabellen efter anslutande ExR och virtuella nätverk"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso VM vägar efter flera anslutande ExR och virtuella nätverk"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam VM vägar efter flera anslutande ExR och virtuella nätverk"
[14]: ./media/cross-network-connectivity/globalreach.png "the arkitektur när du har konfigurerat Global räckvidd"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute routningstabellen efter Global räckvidd"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute routningstabellen efter Global räckvidd"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq