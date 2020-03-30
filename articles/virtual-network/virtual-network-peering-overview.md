---
title: Azure Virtual Network-peering
titlesuffix: Azure Virtual Network
description: Lär dig mer om virtuell nätverkspeering i Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279551"
---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Med virtuell nätverks peering kan du sömlöst ansluta nätverk i [Azure Virtual Network](virtual-networks-overview.md). De virtuella nätverken visas som ett för anslutningsändamål. Trafiken mellan virtuella datorer använder Microsofts stamnätsinfrastruktur. Precis som trafik mellan virtuella datorer i samma nätverk dirigeras endast trafiken via Microsofts *privata* nätverk.

Azure stöder följande typer av peering:

* Virtuell nätverks peering: Anslut virtuella nätverk inom samma Azure-region.
* Global virtuell nätverks peering: Ansluta virtuella nätverk över Azure-regioner.

Fördelar med att använda VNET-peering (avsett om den är lokal eller global):

* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Möjligheten för resurser i ett virtuellt nätverk att kommunicera med resurser i ett annat virtuellt nätverk.
* Möjligheten att överföra data mellan virtuella nätverk över Azure-prenumerationer, Azure Active Directory-klienter, distributionsmodeller och Azure-regioner.
* Möjligheten att peer virtuella nätverk som skapats via Azure Resource Manager.
* Möjligheten att peer ett virtuellt nätverk som skapats via Resource Manager till en som skapats genom den klassiska distributionsmodellen. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Inga driftstopp för resurser i de virtuella nätverken när du skapar peer-kopplingen eller när peer-kopplingen har skapats.

Nätverkstrafiken mellan peer-kopplade virtuella nätverk är privat. Trafiken mellan de virtuella nätverken finns i Microsoft-stamnätverket. Vid kommunikation mellan virtuella nätverk krävs inget offentligt Internet, inga gatewayer eller ingen kryptering.

## <a name="connectivity"></a>Anslutning

För peer-peer-virtuella nätverk kan resurser i båda virtuella nätverken ansluta direkt till resurser i det peer-inkopplade virtuella nätverket.

Nätverksfördröjningen mellan virtuella datorer i peer-kopplade virtuella nätverk i samma region är densamma som svarstiden inom ett enda virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt genom Microsoft-stamnätsinfrastrukturen, inte via en gateway eller det offentliga Internet.

Du kan använda nätverkssäkerhetsgrupper i antingen virtuellt nätverk för att blockera åtkomst till andra virtuella nätverk eller undernät.
När du konfigurerar virtuell nätverks peering ska du antingen öppna eller stänga reglerna för nätverkssäkerhetsgruppen mellan de virtuella nätverken. Om du öppnar fullständig anslutning mellan peer-ed virtuella nätverk kan du använda nätverkssäkerhetsgrupper för att blockera eller neka specifik åtkomst. Fullständig anslutning är standardalternativet. Mer information om nätverkssäkerhetsgrupper finns i [Säkerhetsgrupper](security-overview.md).

## <a name="service-chaining"></a>Tjänstlänkning

Med servicekedjektering kan du dirigera trafik från ett virtuellt nätverk till en virtuell installation eller gateway i ett peer-nätverk via användardefinierade vägar.

Om du vill aktivera tjänstkedjektering konfigurerar du användardefinierade vägar som pekar på virtuella datorer i peered-virtuella nätverk som *nästa HOP* IP-adress. Användardefinierade vägar kan också peka på virtuella nätverksgateways för att aktivera servicekedjektering.

Du kan distribuera hub-and-spoke-nätverk, där hubbvirt nätverksen är värd för infrastrukturkomponenter som en virtuell nätverksinstallation eller VPN-gateway. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafiken flödar via virtuella nätverksinstallationer eller VPN-gateways i det virtuella navnätverket.

Peering för virtuellt nätverk gör att nästa hopp i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket eller en VPN-gateway. Du kan inte cirkulera mellan virtuella nätverk med en användardefinierad väg som anger en Azure ExpressRoute-gateway som nästa hopptyp. Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Mer information om hur du skapar en hubb och talade nätverkstopologi finns [i Hub-spoke nätverkstopologi i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Varje virtuellt nätverk, inklusive ett peer-inskrivet virtuellt nätverk, kan ha en egen gateway. Ett virtuellt nätverk kan använda sin gateway för att ansluta till ett lokalt nätverk. Du kan också konfigurera [virtuella nätverksanslutningar till virtuella nätverksanslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) med hjälp av gateways, även för peer-ed virtuella nätverk.

När du konfigurerar båda alternativen för sammankoppling av virtuella nätverk flödar trafiken mellan de virtuella nätverken via peering-konfigurationen. Trafiken använder Azure-stamnätet.

Du kan också konfigurera gatewayen i det peer-in-virtuella nätverket som en transitpunkt till ett lokalt nätverk. I det här fallet kan det virtuella nätverket som använder en fjärrgateway inte ha en egen gateway. Ett virtuellt nätverk har bara en gateway. Gatewayen är antingen en lokal eller fjärrgateway i det peer-invaplade virtuella nätverket, vilket visas i följande diagram:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Både virtuell nätverks peering och globala virtuella nätverk peering stöd gateway transitering.

Gateway-överföring mellan virtuella nätverk som skapas genom olika distributionsmodeller stöds. Gatewayen måste finnas i det virtuella nätverket i Resource Manager-modellen. Mer information om hur du använder en gateway för överföring finns i [Configure a VPN gateway for transit in a virtual network peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Konfigurera en VPN-gateway för överföring i peer-kopplade virtuella nätverk).

När du peer virtuella nätverk som delar en enda Azure ExpressRoute-anslutning, går trafiken mellan dem genom peering-relationen. Den trafiken använder Azure-stamnätet. Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Annars kan du använda en delad gateway och konfigurera överföring för lokal anslutning.

## <a name="troubleshoot"></a>Felsöka

Om du vill bekräfta att virtuella nätverk är peer-peer-datorer kan du kontrollera effektiva vägar. Kontrollera vägar för ett nätverksgränssnitt i alla undernät i ett virtuellt nätverk. Om peer-koppling för virtuellt nätverk finns har alla undernät i det virtuella nätverket vägar med nästa hopp-typ *VNet-peering* för varje adressutrymme i varje peer-kopplat virtuellt nätverk. Mer information finns i [Diagnostisera ett routningsproblem för den virtuella datorn](diagnose-network-routing-problem.md).

Du kan också felsöka anslutningen till en virtuell dator i ett peer-virtuellt nätverk med Azure Network Watcher. Med en anslutningskontroll kan du se hur trafiken dirigeras från en källdlydators nätverksgränssnitt till en måldydators nätverksgränssnitt. Mer information finns i [Felsöka anslutningar med Azure Network Watcher med Azure-portalen](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Du kan också prova [felsökningen av problem med virtuella nätverks peering](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Begränsningar för peer-in-virtuella nätverk<a name="requirements-and-constraints"></a>

Följande begränsningar gäller endast när virtuella nätverk peerkopplas globalt:

* Resurser i ett virtuellt nätverk kan inte kommunicera med frontend-IP-adressen för en grundläggande intern belastningsutjämnad (ILB) i ett globalt peer-virtuellt nätverk.
* Vissa tjänster som använder en grundläggande belastningsutjämnare fungerar inte över global virtuell nätverks peering. Mer information finns i [Vilka begränsningar är relaterade till Global VNet Peering och Load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Mer information finns i [Krav och begränsningar](virtual-network-manage-peering.md#requirements-and-constraints). Mer information om antalet peerings som stöds finns i [Nätverksbegränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Behörigheter

Mer information om behörigheter som krävs för att skapa en virtuell nätverks peering finns i [Behörigheter](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prissättning

Det finns en nominell avgift för ingående och utgående trafik som använder en virtuell nätverks peering-anslutning. Mer information finns i [Prissättning för virtuellt nätverk](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit är en peering-egenskap som gör det möjligt för ett virtuellt nätverk att använda en VPN/ExpressRoute-gateway i ett peer-virtuellt nätverk. Gateway transit fungerar för både korslokaler och nätverk-till-nätverk anslutning. Trafik till gatewayen (ingående eller utgående) i det peered virtuella nätverket medför virtuella nätverks peering avgifter på eker VNet (eller icke-gateway VNet). Mer information finns i [VPN Gateway-priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) för VPN-gatewayavgifter och ExpressRoute Gateway-priser för ExpressRoute gateway-avgifter.

>[!NOTE]
> I en tidigare version av det här dokumentet angavs att avgifter för virtuella nätverks peering-datorer inte skulle tillkomma på det ekerbaserade virtuella nätverket (eller vnet som inte är gateway) med Gateway Transit. Det återspeglar nu korrekt prissättning per prissidan.

## <a name="next-steps"></a>Nästa steg

* Du kan skapa en peering mellan två virtuella nätverk. Nätverken kan tillhöra samma prenumeration, olika distributionsmodeller i samma prenumeration eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Mer information om hur du skapar en hubb och talade nätverkstopologi finns [i Hub-spoke nätverkstopologi i Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Mer information om alla inställningar för virtuell nätverks peering finns i [Skapa, ändra eller ta bort en virtuell nätverks peering](virtual-network-manage-peering.md).
* Svar på vanliga frågor om virtuell nätverks peering och globala virtuella nätverks peering finns i [VNet Peering](virtual-networks-faq.md#vnet-peering).
