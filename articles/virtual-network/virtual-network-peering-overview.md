---
title: "Peerkoppling i virtuella nätverk på Azure | Microsoft Docs"
description: "Lär dig mer om virtuell nätverkspeering i Azure."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: narayan
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 393557074db2ddbeb53ca20873a33d06874c4dc8
ms.contentlocale: sv-se
ms.lasthandoff: 07/19/2017

---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering
Virtuell nätverkspeering gör att du kan ansluta två virtuella nätverk i samma region via Azures stamnätverk. När de två virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. De två virtuella nätverken hanteras fortfarande som separata resurser, men virtuella datorer i de peer-kopplade virtuella nätverken kan kommunicera med varandra direkt med hjälp av privata IP-adresser.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras via Azure-infrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk. Några av fördelarna med att använda peering i virtuella nätverk är:

* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Möjlighet att använda resurser, till exempel nätverksinstallationer och VPN-gateways som överföringspunkter i ett peer-kopplat virtuellt nätverk.
* Möjligheten att peerkoppla två virtuella nätverk som har skapats via Azure Resource Manager-distributionsmodellen eller att peerkoppla ett virtuellt nätverk som har skapats via Resource Manager till ett virtuellt nätverk som har skapats via den klassiska distributionsmodellen. Läs artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Förstå Azure-distributionsmodellerna) om du vill ta reda på mer om skillnaderna mellan de två Azure-distributionsmodellerna.

## <a name="requirements-constraints"></a>Det finns vissa krav och begränsningar

* Peerkopplade virtuella nätverk måste finnas i samma Azure-region. Du kan ansluta till virtuella nätverk i olika Azure-regioner med en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Peerkopplade virtuella nätverk får inte ha överlappande IP-adressutrymmen.
* Adressutrymmen kan inte läggas till eller tas bort från ett virtuellt nätverk när ett virtuellt nätverk är peerkopplat med ett annat virtuellt nätverk.
* Peer-kopplingarna i virtuella nätverk upprättas mellan två virtuella nätverk. Det finns ingen härledd transitiv relation mellan peer-kopplingar. Till exempel om virtualNetworkA är peer-kopplat med virtualNetworkB och virtualNetworkB är peer-kopplat med virtualNetworkC är virtualNetwork A *inte* peerkopplat till virtualNetworkC.
* Peerkoppling kan upprättas mellan virtuella nätverk i två olika prenumerationer under förutsättning att en privilegierad användare (se [speciella tillstånd](create-peering-different-deployment-models-subscriptions.md#permissions)) av båda prenumerationerna tillåter peerkopplingen, och prenumerationerna är kopplade till samma Azure Active Directory-klient. Du kan använda en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta virtuella nätverk i prenumerationer som är kopplade till olika Active Directory-klienter.
* Virtuella nätverk kan peerkopplas om båda skapas via distributionsmodellen Resurshanteraren eller om det ena virtuella nätverket skapas via distributionsmodellen Resurshanteraren och det andra skapas via den klassiska distributionsmodellen. Två virtuella nätverk som har skapats via den klassiska distributionsmodellen kan dock inte peerkopplas till varandra. Du kan använda en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) för att ansluta två virtuella nätverk som har skapats via den klassiska distributionsmodellen.
* Även om kommunikationen mellan virtuella datorer i peer-kopplade virtuella nätverk inte har ytterligare bandbreddsbegränsningar finns det en maximal nätverksbandbredd beroende på storleken på den virtuella datorn som fortfarande gäller. Om du vill veta mer om nätverkets maximala bandbredd för olika virtuella datorstorlekar läser du artiklarna om virtuella datorstorlekar i [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Azures interna DNS-namnmatchning för virtuella datorer fungerar inte mellan peer-kopplade virtuella nätverk. Virtuella datorer har interna DNS-namn som bara matchas i det virtuella lokala nätverket. Du kan dock konfigurera virtuella datorer som är anslutna till peer-kopplade virtuella nätverk som DNS-servrar för ett virtuellt nätverk. Läs artikeln [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) (Namnmatchning med hjälp av en egen DNS-server) för mer information.

![Grundläggande virtuell nätverkspeering](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Anslutning
När två virtuella nätverk har peer-kopplats kan resurser i de virtuella nätverken ansluta till resurser it det peer-kopplade virtuella nätverket. Dessa två virtuella nätverk kan upprätta fullständiga anslutningar på IP-nivå.

Svarstiden i nätverk för kommunikation (tur och retur) mellan två virtuella datorer i peer-kopplade virtuella nätverk är samma som den i ett lokalt virtuellt nätverk. Nätverkets genomflöde baseras på den bandbredd som tillåts för den virtuella datorn i proportion till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peer-kopplade virtuella nätverk dirigeras direkt via Azures backend-infrastruktur och inte genom en gateway.

Virtuella datorer i ett virtuellt nätverk har åtkomst till slutpunkterna för interna belastningsutjämnare i det peer-kopplade virtuella nätverket. Nätverkssäkerhetsgrupper kan användas i något av de virtuella nätverken för att blockera åtkomsten till andra virtuella nätverk eller till undernät om det behövs.

När du konfigurerar peering i virtuella nätverk, kan du öppna eller stänga av reglerna för nätverkssäkerhetsgrupper mellan virtuella nätverk. Om du väljer att öppna fullständiga anslutningar mellan peer-kopplade virtuella nätverk (vilket är standardalternativet) kan du sedan använda nätverkssäkerhetsgrupper för specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst. Mer information om nätverkssäkerhetsgrupper finns i artikeln [Nätverkssäkerhetsöversikt](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Tjänstlänkning
Du kan konfigurera användardefinierade routningstabeller som pekar på virtuella datorer i peer-kopplade virtuella nätverk som ”nästa hopp”-IP-adressen för säker tjänstlänkning. Med säker tjänstlänkning kan du skapa en tjänstlänkning och därigenom dirigera trafik från ett virtuellt nätverk till en virtuell enhet som körs i ett peer-kopplat virtuellt nätverk genom användardefinierade routning.

Du kan även effektivt bygga nav-och-ekermiljöer där hubben kan vara värd för infrastrukturkomponenter, t.ex. en virtuell installation. Alla virtuella ekernätverk kan peer-kopplas till det virtuella navnätverket. Trafiken kan flöda via nätverkets virtuella installationer som körs i det virtuella navnätverket. I korthet gör VNet-peering att ”nästa hopp”-IP-adressen i den användardefinierade routningen kan vara IP-adressen för en virtuell dator i det peer-kopplade virtuella nätverket. Mer information om användardefinierade vägar finns i översiktsartikeln [Användardefinierade vägar](virtual-networks-udr-overview.md).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar
Varje virtuellt nätverk, oavsett om det är peer-kopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha sin egen gateway och använda den för att ansluta till det lokala nätverket. Du kan också konfigurera [VNet-till-VNet-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) genom att använda gateways, även om de virtuella nätverken är peer-kopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peer-kopplats kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. I detta fall kan det virtuella nätverket som använder en fjärr-gateway inte ha sin egen gateway. Ett virtuellt nätverk kan bara ha en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peerkopplade virtuella nätverket), som du ser på följande bild:

![Överföring med VNet-peering](./media/virtual-networks-peering-overview/figure02.png)

Gatewayöverföring stöds inte i peering-förhållandet mellan virtuella nätverk som har skapats via olika distributionsmodeller. Båda de virtuella nätverken i peering-relationen måste ha skapats via resurshanteraren för att en gatewayöverföring ska fungera.

När de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peer-kopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Du kan även använda en delad gateway och konfigurera överföringen för lokala anslutningar.

## <a name="provisioning"></a>Etablering
Virtuell nätverkspeering är en privilegierad åtgärd. Det är en separat funktion under namnområdet VirtualNetworks. En användare kan beviljas specifika rättigheter för att tillåta peering. En användare som har skrivskyddad åtkomst till det virtuella nätverket ärver dessa rättigheter automatiskt.

En användare som antingen är en administratör eller en privilegierad användare av peering-funktionen kan initiera en peering-åtgärd i ett annat virtuellt nätverk. Om det finns en matchande begäran om peering på den andra sidan, och om andra krav är uppfyllda, så upprättas peer-kopplingen.

## <a name="limits"></a>Begränsningar
Det finns begränsningar för antalet peering-sessioner som tillåts för ett enda virtuellt nätverk. Mer information finns i [Nätverksgränser i Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Prissättning
En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Nästa steg

* Slutför självstudien om peering för virtuella nätverk. Peering för virtuellt nätverk skapas mellan virtuella nätverk som skapats via samma eller olika distributionsmodeller som finns i samma eller olika prenumerationer. Genomför en självstudiekurs för något av följande scenarier:
 
    |Azure-distributionsmodell  | Prenumeration  |
    |---------|---------|
    |Båda Resource Manager |[Samma](virtual-network-create-peering.md)|
    | |[Olika](create-peering-different-subscriptions.md)|
    |En Resource Manager, en klassisk     |[Samma](create-peering-different-deployment-models.md)|
    | |[Olika](create-peering-different-deployment-models-subscriptions.md)|

* Lär dig hur du skapar en [nätverkstopologi med nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 
* Lär dig mer om alla [peering-inställningar för virtuella nätverk och hur du ändrar dem](virtual-network-manage-peering.md)

