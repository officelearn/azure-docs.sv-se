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
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Virtuell nätverkspeering
Virtuell nätverkspeering gör att du kan ansluta två virtuella nätverk i samma region via Azures stamnätverk. När de två virtuella nätverken har peerkopplats visas de som ett nätverk för anslutningsändamål. De två virtuella nätverken hanteras fortfarande som separata resurser, men virtuella datorer i dessa peerkopplade virtuella nätverk kan kommunicera med varandra direkt med hjälp av privata IP-adresser.

Trafiken mellan virtuella datorer i peerkopplade virtuella nätverk dirigeras via Azure-infrastrukturen på nästan samma sätt som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk. Några av fördelarna med att använda VNet-peering är:

* En anslutning med korta svarstider och hög bandbredd mellan resurser i olika virtuella nätverk.
* Möjlighet att använda resurser, till exempel nätverksinstallationer och VPN-gateways som överföringspunkter i ett peer-kopplat VNet.
* Möjligheten att peerkoppla två virtuella nätverk som har skapats via Azure Resource Manager-distributionsmodellen eller att peerkoppla ett virtuellt nätverk som har skapats via Resource Manager till ett virtuellt nätverk som har skapats via den klassiska distributionsmodellen. Läs artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Förstå Azure-distributionsmodellerna) om du vill ta reda på mer om skillnaderna mellan de två Azure-distributionsmodellerna.

Krav och viktiga aspekter relaterade till VNet-peering:

* Peerkopplade virtuella nätverk måste finnas i samma Azure-region.
* Peerkopplade virtuella nätverk får inte ha överlappande IP-adressutrymmen.
* Virtuell nätverkspeering sker mellan två virtuella nätverk, men det finns ingen härledd transitiv relation mellan peerkopplingar. Om exempelvis VNetA peerkopplas till VNetB och VNetB peerkopplas till VNetC, är VNetA *inte* peerkopplat till VNetC.
* Peerkoppling kan upprättas mellan virtuella nätverk i två olika prenumerationer under förutsättning att en privilegierad användare av båda prenumerationerna tillåter peerkopplingen, och prenumerationerna är kopplade till samma Active Directory-klient.
* Virtuella nätverk kan peerkopplas om båda skapas via distributionsmodellen Resurshanteraren eller om det ena skapas via distributionsmodellen Resurshanteraren och det andra skapas via den klassiska distributionsmodellen. Två virtuella nätverk som har skapats via den klassiska distributionsmodellen kan dock inte peerkopplas till varandra. Om de peerkopplade virtuella nätverken har skapats via olika distributionsmodeller måste båda de virtuella nätverken finnas i *samma* prenumeration. Möjligheten att peerkoppla virtuella nätverk som har skapats via de olika distributionsmodeller som finns i *olika* prenumerationer finns i **förhandsgransknings**versionen. Läs artikeln [Create a virtual network peering using Powershell](virtual-networks-create-vnetpeering-arm-ps.md) (Skapa en virtuell nätverkspeering med Powershell) för mer information.
* Även om kommunikationen mellan virtuella datorer i peerkopplade virtuella nätverk inte har ytterligare bandbreddsbegränsningar finns det en maximal nätverksbandbredd beroende på storleken på den virtuella datorn som fortfarande gäller. Om du vill veta mer om nätverkets maximala bandbredd för olika virtuella datorstorlekar läser du artiklarna om virtuella datorstorlekar i [Windows](../virtual-machines/windows/sizes.md) eller [Linux](../virtual-machines/linux/sizes.md).

![Grundläggande VNet-peering](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Anslutning
När två virtuella nätverk är peerkopplade kan virtuella datorer eller molntjänstroller i det virtuella nätverket ansluta direkt till andra resurser kopplade till det peerkopplade virtuella nätverket. Dessa två virtuella nätverk kan upprätta fullständiga anslutningar på IP-nivå.

Svarstiden i nätverk för kommunikation (tur och retur) mellan två virtuella datorer i peerkopplade virtuella nätverk är identisk med svarstiden i ett enskilt virtuellt nätverk. Nätverkets dataflöde baseras på den bandbredd som tillåts för den virtuella datorn i förhållande till dess storlek. Det finns inte några ytterligare begränsning vad gäller bandbredden inom peerkopplingen.

Trafiken mellan virtuella datorer i peerkopplade virtuella nätverk dirigeras direkt via Azures interna infrastruktur och inte genom en gateway.

Virtuella datorer som är anslutna till ett virtuellt nätverk har åtkomst till de interna belastningsutjämnade slutpunkterna i det peerkopplade virtuella nätverket. Nätverkssäkerhetsgrupper (NSG:er) kan användas antingen i det virtuella nätverket för att blockera åtkomsten till andra virtuella nätverk eller till undernät om det behövs.

När du konfigurerar peering kan du antingen öppna eller stänga NSG-reglerna mellan de virtuella nätverken. Om du öppnar den fullständiga anslutningen mellan peerkopplade virtuella nätverk (vilket är standardalternativet) kan tillämpa nätverkssäkerhetsgrupper på specifika undernät eller virtuella datorer för att blockera eller neka specifik åtkomst. Mer information om nätverkssäkerhetsgrupper finns i artikeln [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md).

Azures interna DNS-namnmatchning för virtuella datorer fungerar inte mellan peerkopplade virtuella nätverk. Virtuella datorer har interna DNS-namn som bara kan matchas i det lokala virtuella nätverket. Du kan dock konfigurera virtuella datorer som är anslutna till peerkopplade virtuella nätverk som DNS-servrar för ett virtuellt nätverk. Läs artikeln [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) (Namnmatchning med hjälp av en egen DNS-server) för mer information.

## <a name="service-chaining"></a>Tjänstlänkning
Du kan konfigurera användardefinierade vägar (UDR:er) som pekar på virtuella datorer i peerkopplade virtuella nätverk som nästa hopp för IP-adressen, vilket visas i diagrammet senare i den här artikeln. Detta möjliggör tjänstlänkning, vilket gör att du att dirigera trafiken från ett virtuellt nätverk till en virtuell installation som körs i ett peerkopplat virtuellt nätverk via de användardefinierade vägarna.

Du kan även effektivt bygga nav-och-ekermiljöer där hubben kan vara värd för infrastrukturkomponenter, t.ex. en virtuell installation. Alla dessa virtuella ekernätverk kan sedan peerkopplas med den, liksom en delmängd av trafiken, till enheter som körs i hubbens virtuella nätverk. Virtuell nätverkspeering gör kort sagt att nästa hopp för IP-adressen i den användardefinierade vägen är IP-adressen för en virtuell dator i det peerkopplade virtuella nätverket. Läs artikeln om [användardefinierade vägar](virtual-networks-udr-overview.md) för mer information om användardefinierade vägar.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways och lokala anslutningar
Varje virtuellt nätverk, oavsett om det är peerkopplat med ett annat virtuellt nätverk eller inte, kan fortfarande ha en egen gateway och använda den för att ansluta till ett lokalt nätverk. Användarna kan också konfigurera [anslutningar mellan virtuella nätverk](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) genom att använda gateways, även om de virtuella nätverken är peerkopplade.

När båda alternativen för anslutningar mellan virtuella nätverk har konfigurerats flödar trafiken mellan de virtuella nätverken genom peering-konfigurationen (det vill säga genom Azures stamnätverk).

När virtuella nätverk har peerkopplats kan användarna också konfigurera gatewayen i det peerkopplade virtuella nätverket som en överföringspunkt till ett lokalt nätverk. I detta fall kan det virtuella nätverk som använder en fjärrgateway inte ha sin egen gateway. Ett virtuellt nätverk kan ha bara en gateway. Det kan antingen vara en lokal gateway eller en fjärr-gateway (i det peerkopplade virtuella nätverket), som du ser på följande bild:

![Överföring med VNet-peering](./media/virtual-networks-peering-overview/figure02.png)

Gatewayöverföring stöds inte i peering-förhållandet mellan virtuella nätverk som har skapats via olika distributionsmodeller. Båda de virtuella nätverken i peering-relationen måste ha skapats via resurshanteraren för att en gatewayöverföring ska fungera.

Om de virtuella nätverken som delar samma Azure ExpressRoute-anslutning är peerkopplade går trafiken mellan dem genom peering-relationen (dvs. genom Azures stamnätverk). Du kan fortfarande använda lokala gateways i varje virtuellt nätverk för att ansluta till den lokala kretsen. Du kan även använda en delad gateway och konfigurera överföringen för lokala anslutningar.

## <a name="provisioning"></a>Etablering
VNet-peering är en privilegierad åtgärd. Det är en separat funktion under namnområdet VirtualNetworks. En användare kan beviljas specifika rättigheter för att tillåta peering. En användare som har skrivskyddad åtkomst till det virtuella nätverket ärver dessa rättigheter automatiskt.

En användare som antingen är en administratör eller en privilegierad användare av peering-funktionen kan initiera en peering-åtgärd i ett annat VNet. Om det finns en matchande begäran om peering på den andra sidan, och om andra krav är uppfyllda, så upprättas peer-kopplingen.

Läs artiklarna i avsnittet [Nästa steg](#next-steps) i den här artikeln om du vill veta mer om hur du etablerar virtuell nätverkspeering mellan två virtuella nätverk.

## <a name="limits"></a>Begränsningar
Det finns begränsningar för antalet peering-sessioner som tillåts för ett enda virtuellt nätverk. Mer information finns i [Nätverksgränser i Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Priser
En nominell avgift tas ut för ingående och utgående trafik som använder virtuell nätverks-peering. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Nästa steg
Lär dig att skapa en virtuell nätverkspeering med hjälp av:

* [Azure Portal](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [En Azure Resource Manager-mall](virtual-networks-create-vnetpeering-arm-template-click.md)

