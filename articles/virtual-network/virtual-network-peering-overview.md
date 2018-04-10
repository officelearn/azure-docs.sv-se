---
title: Peerkoppling i virtuella nätverk på Azure | Microsoft Docs
description: Lär dig mer om virtuell nätverkspeering i Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: jdial
ms.openlocfilehash: 69264ac7ffe8124b964a10c11aacaca5c712fd63
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Med VNET-peering kan du smidigt ansluta två [virtuella Azure-nätverk](virtual-networks-overview.md). När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras via Microsoft-stamnätsinfrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk genom endast *privata* IP-adresser. 

Fördelarna med att använda VNET-peering är:

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
När du konfigurerar peering i virtuella nätverk, kan du öppna eller stänga av reglerna för nätverkssäkerhetsgrupper mellan virtuella nätverk. Om du väljer att öppna fullständiga anslutningar mellan peer-kopplade virtuella nätverk (vilket är standardalternativet) kan du sedan använda nätverkssäkerhetsgrupper för specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst. Mer information om nätverkssäkerhetsgrupper finns i [översikten över nätverkssäkerhet](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Tjänstlänkning

Du kan konfigurera användardefinierade routningstabeller som pekar på virtuella datorer i peer-kopplade virtuella nätverk som IP-adressen för *nästa hopp* eller till virtuella nätverksgatewayer för säker tjänstlänkning. Med säker tjänstlänkning kan du skapa en tjänstlänkning och därigenom dirigera trafik från ett virtuellt nätverk till en virtuell enhet eller virtuella nätverskgatewayer som körs i ett peer-kopplat virtuellt nätverk genom användardefinierade routning.

Du kan distribuera nav-och-eker-nätverk där det virtuella navnätverket kan vara värd för infrastrukturkomponenter, t.ex. en virtuell installation eller en VPN-gateway. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafiken kan flöda via nätverkets virtuella installationer eller VPN-gatewayer i det virtuella navnätverket. 

Peering för virtuellt nätverk gör att nästa hopp i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket eller en VPN-gateway. Du kan dock inte gå mellan virtuella nätverk via en användardefinierad väg som anger en ExpressRoute-gateway som nästa hopp-typ. Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined). Läs informationen om [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering) om du vill lära dig hur du skapar en nätverkstopologi med nav och ekrar.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar

Varje virtuellt nätverk, oavsett om det är peer-kopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha sin egen gateway och använda den för att ansluta till det lokala nätverket. Du kan också konfigurera [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genom att använda gateways, även om de virtuella nätverken är peer-kopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peer-kopplats i samma region kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. I detta fall kan det virtuella nätverket som använder en fjärr-gateway inte ha sin egen gateway. Ett virtuellt nätverk kan bara ha en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peerkopplade virtuella nätverket), som du ser på följande bild:

![Överföring vid VNET-peering](./media/virtual-networks-peering-overview/figure04.png)

Gatewayöverföring stöds inte i peering-förhållandet mellan virtuella nätverk som har skapats via olika distributionsmodeller eller i olika regioner. Båda de virtuella nätverken i peering-relationen måste ha skapats via Resource Manager och måste vara i samma region för att en gatewayöverföring ska fungera.

När de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peer-kopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Du kan även använda en delad gateway och konfigurera överföringen för lokala anslutningar.

## <a name="troubleshoot"></a>Felsöka

För att bekräfta peer-kopplingen för virtuella nätverk, kan du [kontrollera effektiva vägar](virtual-network-routes-troubleshoot-portal.md) för ett nätverksgränssnitt i alla undernät i ett virtuellt nätverk. Om peer-koppling för virtuellt nätverk finns har alla undernät i det virtuella nätverket vägar med nästa hopp-typ *VNet-peering* för varje adressutrymme i varje peer-kopplat virtuellt nätverk.

Du kan även felsöka anslutningen till en virtuell dator i ett peer-kopplat virtuellt nätverk med Network Watchers [anslutningskontroll](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Med anslutningskontrollen kan du se hur trafik vidarebefordras från en virtuell källdators nätverksgränssnitt till en virtuell måldators nätverksgränssnitt.

## <a name="requirements-and-constraints"></a>Krav och begränsningar

Läs om krav och begränsningar i avsnittet om [krav och begränsningar för peering för virtuella nätverk](virtual-network-manage-peering.md#requirements-and-constraints). Du kan läsa om gränserna för hur många peerkopplingar du kan skapa för ett virtuellt nätverk i [Nätverksbegränsningar för Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Behörigheter

Du kan läsa om behörigheterna som krävs för att skapa peering för ett virtuellt nätverk i [Peeringbehörigheter i virtuella nätverk](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prissättning

En nominell avgift tas ut för ingående och utgående trafik som använder en VNET-peeringanslutning. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Nästa steg

* Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:

    |Azure-distributionsmodell             | Prenumeration  |
    |---------                          |---------|
    |Båda Resource Manager              |[Samma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk  |[Samma](create-peering-different-deployment-models.md)|
    |                                   |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Lär dig mer om alla [peering-inställningar för virtuella nätverk och hur du ändrar dem](virtual-network-manage-peering.md).