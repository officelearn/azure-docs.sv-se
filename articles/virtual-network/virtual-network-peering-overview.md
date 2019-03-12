---
title: Azure Virtual Network-peering
titlesuffix: Azure Virtual Network
description: Lär dig mer om virtuell nätverkspeering i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: 5687075b8b63755b8b04f8c8fd0d0706ec8e27bc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774531"
---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Virtuell nätverkspeering gör det möjligt för dig att sömlöst ansluta Azure [virtuella nätverk](virtual-networks-overview.md). När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras via Microsoft-stamnätsinfrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk genom endast *privata* IP-adresser. Azure stöder:
* VNet-peering – anslutning av virtuella nätverk i samma Azure-region
* Global VNet-peering – anslutning av virtuella nätverk i olika Azure-regioner

Fördelar med att använda VNET-peering (avsett om den är lokal eller global):

* Nätverkstrafiken mellan peer-kopplade virtuella nätverk är privat. Trafiken mellan de virtuella nätverken finns i Microsoft-stamnätverket. Vid kommunikation mellan virtuella nätverk krävs inget offentligt Internet, inga gatewayer eller ingen kryptering.
* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Funktionen för att resurser i ett virtuellt nätverk ska kunna kommunicera med resurser i ett annat virtuellt nätverk när de virtuella nätverken är peer-kopplade.
* Möjlighet att överföra data mellan Azure-prenumerationer, distributionsmodeller och Azure-regioner.
* Möjligheten att peer-koppla virtuella nätverk som har skapats via Azure Resource Manager eller att peer-koppla ett virtuellt nätverk som har skapats via Resource Manager till ett virtuellt nätverk som har skapats via den klassiska distributionsmodellen. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Inga driftstopp för resurser i de virtuella nätverken när du skapar peer-kopplingen eller när peer-kopplingen har skapats.

## <a name="connectivity"></a>Anslutning

När virtuella nätverk har peer-kopplats kan resurser i de virtuella nätverken ansluta till resurser it det peer-kopplade virtuella nätverket.

Nätverksfördröjningen mellan virtuella datorer i peer-kopplade virtuella nätverk i samma region är densamma som svarstiden inom ett enda virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt genom Microsoft-stamnätsinfrastrukturen, inte via en gateway eller det offentliga Internet.

Nätverkssäkerhetsgrupper kan användas i något av de virtuella nätverken för att blockera åtkomsten till andra virtuella nätverk eller till undernät om det behövs.
När du konfigurerar peering i virtuella nätverk, kan du öppna eller stänga av reglerna för nätverkssäkerhetsgrupper mellan virtuella nätverk. Om du väljer att öppna fullständiga anslutningar mellan peer-kopplade virtuella nätverk (vilket är standardalternativet) kan du sedan använda nätverkssäkerhetsgrupper för specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst. Mer information om nätverkssäkerhetsgrupper finns i [översikten över nätverkssäkerhet](security-overview.md).

## <a name="service-chaining"></a>Tjänstlänkning

Du kan konfigurera användardefinierade routningstabeller som pekar på virtuella datorer i peer-kopplade virtuella nätverk som IP-adressen för *nästa hopp* eller till virtuella nätverksgatewayer för säker tjänstlänkning. Med säker tjänstlänkning kan du skapa en tjänstlänkning och därigenom dirigera trafik från ett virtuellt nätverk till en virtuell enhet eller virtuella nätverskgatewayer som körs i ett peer-kopplat virtuellt nätverk genom användardefinierade routning.

Du kan distribuera nav-och-eker-nätverk där det virtuella navnätverket kan vara värd för infrastrukturkomponenter, t.ex. en virtuell installation eller en VPN-gateway. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafiken kan flöda via nätverkets virtuella installationer eller VPN-gatewayer i det virtuella navnätverket. 

Peering för virtuellt nätverk gör att nästa hopp i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket eller en VPN-gateway. Du kan dock inte gå mellan virtuella nätverk via en användardefinierad väg som anger en ExpressRoute-gateway som nästa hopp-typ. Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Läs informationen om [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) om du vill lära dig hur du skapar en nätverkstopologi med nav och ekrar.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Varje virtuellt nätverk, oavsett om det är peer-kopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha sin egen gateway och använda den för att ansluta till det lokala nätverket. Du kan också konfigurera [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genom att använda gateways, även om de virtuella nätverken är peer-kopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peer-kopplats i samma region kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. I detta fall kan det virtuella nätverket som använder en fjärr-gateway inte ha sin egen gateway. Ett virtuellt nätverk kan bara ha en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peerkopplade virtuella nätverket), som du ser på följande bild:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/figure04.png)

Gatewayöverföring stöds inte i peer-relationer mellan virtuella nätverk som skapats i olika regioner. Båda virtuella nätverk i peer-relationen måste finnas i samma region för att gatewayöverföringen ska fungera. Gatewayöverföring mellan virtuella nätverk som skapats med olika distributionsmodeller (Resource Manager och klassisk) stöds endast om gatewayen (VPN eller ExpressRoute) finns i det virtuella nätverket (Resource Manager). Mer information om hur du använder en gateway för överföring finns i [Configure a VPN gateway for transit in a virtual network peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Konfigurera en VPN-gateway för överföring i peer-kopplade virtuella nätverk).

När de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peer-kopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Du kan även använda en delad gateway och konfigurera överföringen för lokala anslutningar.

## <a name="troubleshoot"></a>Felsöka

För att bekräfta peer-kopplingen för virtuella nätverk, kan du [kontrollera effektiva vägar](diagnose-network-routing-problem.md) för ett nätverksgränssnitt i alla undernät i ett virtuellt nätverk. Om peer-koppling för virtuellt nätverk finns har alla undernät i det virtuella nätverket vägar med nästa hopp-typ *VNet-peering* för varje adressutrymme i varje peer-kopplat virtuellt nätverk.

Du kan även felsöka anslutningen till en virtuell dator i ett peer-kopplat virtuellt nätverk med Network Watchers [anslutningskontroll](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Med anslutningskontrollen kan du se hur trafik vidarebefordras från en virtuell källdators nätverksgränssnitt till en virtuell måldators nätverksgränssnitt.

Du kan också prova att gå igenom [felsökningsstegen för problem relaterade till VNet-peering](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Krav och begränsningar

Följande begränsningar gäller endast när virtuella nätverk peerkopplas globalt:
- Resurser i ett virtuellt nätverk kan inte kommunicera med frontend IP-adressen för en intern belastningsutjämnare i ett globalt peer-kopplade virtuella nätverk. Det finns bara stöd för grundläggande belastningsutjämnare inom samma region. Det finns stöd för Standard Load Balancer för både, Global VNet-Peering och VNet-Peering. 
- Du kan inte använda fjärrgatewayer eller tillåta gatewayöverföring. Om du vill använda fjärrgatewayer eller tillåta gatewayöverföring måste peerkopplade virtuella nätverk finnas i samma region.

Läs mer om krav och begränsningar i avsnittet om [krav och begränsningar för peering för virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints). Du kan läsa om gränserna för hur många peerkopplingar du kan skapa för ett virtuellt nätverk i [Nätverksbegränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Behörigheter

Du kan läsa om behörigheterna som krävs för att skapa peering för ett virtuellt nätverk i [Peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prissättning

En nominell avgift tas ut för ingående och utgående trafik som använder en VNET-peeringanslutning. Mer information om prissättning för VNet-Peering och Global VNet-peering finns på [prissättningssidan](https://azure.microsoft.com/pricing/details/virtual-network).

Gatewayöverföring är en egenskap i peer-kopplade virtuella nätverk som gör att ett virtuellt nätverk kan att använda VPN-gatewayen i ett peer-kopplat virtuellt nätverk för anslutning mellan olika platser eller mellan olika virtuella nätverk. Trafik som går genom en fjärrgateway i det här scenariot omfattas av [avgifter för VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) och medför inga [avgifter för VNet-peering](https://azure.microsoft.com/pricing/details/virtual-network). Om VNetA till exempel har en VPN-gateway för lokal anslutning och VNetB är peer-kopplad till VNetA med lämpliga konfigurerade egenskaper debiteras trafik från VNetB till lokalt endast utgående enligt prissättningen för VPN-gateway. Avgifter för VNet-peering gäller inte. Lär dig hur du [konfigurerar VPN-gatewayöverföring för peer-kopplade virtuella nätverk](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

* Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Lär dig mer om alla [peering-inställningar för virtuella nätverk och hur du ändrar dem](virtual-network-manage-peering.md).
* Få svar på vanliga frågor om VNet Peering och Global VNet Peering via våra [Vanliga frågor och svar om VNet Peering](virtual-networks-faq.md#vnet-peering)
