---
title: 'Arkitektur: Global transitnätverksarkitektur'
titleSuffix: Azure Virtual WAN
description: Lär dig mer om global transitnätverksarkitektur för Virtuellt WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064979"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Global transitnätverksarkitektur och Virtuellt WAN

Moderna företag kräver allestädes närvarande anslutning mellan hyperutdelade program, data och användare i molnet och lokalt. Global transitnätverksarkitektur används av företag för att konsolidera, ansluta och kontrollera det molncentrerade moderna, globala IT-fotavtrycket för företag.

Den globala transitnätverksarkitekturen bygger på en klassisk hub-and-spoke-anslutningsmodell där molnet värdbaserade nätverket "hub" möjliggör transitiv anslutning mellan slutpunkter som kan distribueras över olika typer av "ekrar".

I denna modell kan en eker vara:
* Virtuellt nätverk (virtuella nätverk)
* Fysisk filialplats
* Fjärranvändare
* Internet

![nav och talade](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figur 1: Globalt nätverk för knutpunkter för transitering och eker**

Bild 1 visar den logiska vyn för det globala transitnätverket där geografiskt distribuerade användare, fysiska platser och virtuella nätverk är sammankopplade via ett nätverksnav som finns i molnet. Den här arkitekturen möjliggör logisk en-hop transitanslutning mellan nätverksslutpunkterna.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globalt transitnätverk med virtuellt WAN

Azure Virtual WAN är en Microsoft-hanterad molnnätverkstjänst. Alla nätverkskomponenter som den här tjänsten består av är värd och hanteras av Microsoft. Mer information om Virtuellt WAN finns i artikeln [Om översikt för virtuellt WAN.](virtual-wan-about.md)

Azure Virtual WAN möjliggör en global transitnätverksarkitektur genom att aktivera allestädes närvarande, valfri anslutning mellan globalt distribuerade uppsättningar av molnarbetsbelastningar i virtuella nätverk, filialplatser, SaaS- och PaaS-program och användare.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figur 2: Globalt transitnätverk och virtuellt WAN**

I Azure Virtual WAN-arkitekturen etableras virtuella WAN-hubbar i Azure-regioner som du kan välja att ansluta dina grenar, virtuella nätverk och fjärranvändare till. De fysiska filialplatserna är anslutna till navet av Premium ExpressRoute eller plats-till-plats-VPN, virtuella nätverk är anslutna till navet via VNet-anslutningar och fjärranvändare kan ansluta direkt till navet med Hjälp av User VPN (point-to-site VPN). Virtuellt WAN stöder också VNet-anslutning över flera regioner där ett virtuellt nätverk i en region kan anslutas till en virtuell WAN-hubb i en annan region.

Du kan upprätta ett virtuellt WAN genom att skapa ett enda virtuellt WAN-nav i regionen som har det största antalet ekrar (grenar, virtuella nätverk, användare) och sedan ansluta ekrar som finns i andra regioner till navet. Detta är ett bra alternativ när ett företag fotavtryck är mestadels i en region med några avlägsna ekrar.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Anslutning mellan nav och nav

Ett företagsmolnavtryck kan sträcka sig över flera molnområden och det är optimalt (latensmässigt) att komma åt molnet från en region som ligger närmast deras fysiska webbplats och användare. En av de viktigaste principerna för global transitnätverksarkitektur är att möjliggöra anslutning mellan regioner mellan alla moln och lokala nätverksslutpunkter. Det innebär att trafik från en gren som är ansluten till molnet i en region kan nå en annan gren eller ett virtuella nätverk i en annan region med hjälp av hub-to-hub-anslutning som aktiveras av [Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/).

![tvärregion över region](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Bild 3: Anslutning mellan virtuella WAN-regioner**

När flera hubbar är aktiverade i ett enda virtuellt WAN kopplas naven automatiskt samman via hub-to-hub-länkar, vilket möjliggör global anslutning mellan grenar och virtuella nätverk som distribueras över flera regioner. 

Dessutom kan hubbar som alla ingår i samma virtuella WAN associeras med olika regionala åtkomst- och säkerhetsprinciper. Mer information finns i [Säkerhets- och principkontroll](#security) senare i den här artikeln.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Alla anslutningar som helst

Global transitnätverksarkitektur möjliggör alla anslutningar via virtuella WAN-hubbar. Den här arkitekturen eliminerar eller minskar behovet av fullständig mesh- eller partiell mesh-anslutning mellan ekrar, som är mer komplexa att bygga och underhålla. Dessutom är routningskontroll i nav-och-ekrar jämfört med mesh-nätverk lättare att konfigurera och underhålla.

All-till-alla-anslutning (i samband med en global arkitektur) gör det möjligt för ett företag med globalt distribuerade användare, grenar, datacenter, virtuella nätverk och program att ansluta till varandra via "transit"-hubben. Azure Virtual WAN fungerar som det globala transitsystemet.

![någon till någon till någon](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Bild 4: Virtuella WAN-trafikvägar**

Azure Virtual WAN stöder följande globala transitanslutningssökvägar. Bokstäverna inom parentes mappas till figur 4.

* Filial till VNet (a)
* Gren-till-gren (b)
  * ExpressRoute Global Reach och Virtual WAN
* Fjärranvändare till VNet (c)
* Fjärranvändare-till-gren (d)
* VNet-till-VNet (e)
* Gren-till-hub-hub-till-gren (f)
* Gren-till-hub-hub-till-VNet (g)
* VNet-till-hub-hub-till-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Gren-till-VNet (a) och Korsregion för gren till VNet (g)

Branch-to-VNet är den primära sökvägen som stöds av Azure Virtual WAN. Med den här sökvägen kan du ansluta grenar till Azure IAAS-företagsarbetsbelastningar som distribueras i Virtuella Azure-nätverk. Grenar kan anslutas till den virtuella WAN via ExpressRoute eller plats-till-plats VPN. Trafiken överförs till virtuella nätverk som är anslutna till de virtuella WAN-hubbar via virtuella nätverk. Explicit [gatewaytransitering](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) krävs inte för Virtuellt WAN eftersom Virtuellt WAN automatiskt aktiverar gatewaytransit till filialplats. Se artikel [om Virtuella WAN-partner](virtual-wan-configure-automation-providers.md) om hur du ansluter en SD-WAN-CPE till Virtual WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach och Virtual WAN

ExpressRoute är ett privat och motståndskraftigt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Virtual WAN stöder Express Route-kretsanslutningar. För att ansluta en filialplats till Virtual WAN med Express Route krävs 1) Premium Circuit 2) Circuit för att vara på en global räckviddsaktiverad plats.

ExpressRoute Global Reach är en tilläggsfunktion för ExpressRoute. Med Global Reach kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. Grenar som är anslutna till Azure Virtual WAN med ExpressRoute kräver Att ExpressRoute Global Reach kommunicerar med varandra.

I den här modellen kan varje gren som är ansluten till den virtuella WAN-hubben med ExpressRoute ansluta till virtuella nätverk med hjälp av sökvägen mellan gren och VNet. Förgrena sig i trafiken överförs inte navet eftersom ExpressRoute Global Reach möjliggör en mer optimal sökväg över Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Gren-till-gren (b) och gren-till-gren korsregion (f)

Grenar kan anslutas till en virtuell AZURE WAN-hubb med ExpressRoute-kretsar och/eller VPN-anslutningar för plats till plats. Du kan ansluta grenarna till den virtuella WAN-hubben som finns i den region som ligger närmast grenen.

Med det här alternativet kan företag utnyttja Azure-stamnätet för att ansluta grenar. Även om den här funktionen är tillgänglig bör du dock väga fördelarna med att ansluta grenar över Azure Virtual WAN jämfört med ett privat WAN.  

### <a name="remote-user-to-vnet-c"></a>Fjärranvändare till VNet (c)

Du kan aktivera direkt, säker fjärråtkomst till Azure med point-to-site-anslutning från en fjärranvändarklient till ett virtuellt WAN. Företags fjärranvändare behöver inte längre hårnåla till molnet med hjälp av ett företags-VPN.

### <a name="remote-user-to-branch-d"></a>Fjärranvändare-till-gren (d)

Med sökvägen för fjärranvändare till gren kan fjärranvändare som använder en point-to-site-anslutning till Azure komma åt lokala arbetsbelastningar och program genom att transitera via molnet. Den här sökvägen ger fjärranvändare flexibiliteten att komma åt arbetsbelastningar som både distribueras i Azure och lokalt. Företag kan aktivera central molnbaserad säker fjärråtkomsttjänst i Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-till-VNet-transit (e) och VNet-till-VNet-korsregion (h)

VNet-till-VNet-transiten gör det möjligt för virtuella nätverk att ansluta till varandra för att koppla samman flernivåprogram som implementeras över flera virtuella nätverk. Du kan också ansluta virtuella nätverk till varandra via VNet Peering och detta kan vara lämpligt för vissa scenarier där transitering via VWAN-hubben inte är nödvändig.

## <a name="security-and-policy-control"></a><a name="security"></a>Säkerhets- och principkontroll

Azure Virtual WAN-hubbar kopplar samman alla nätverksslutpunkter över hybridnätverket och kan se all transitnätverkstrafik. Virtuella WAN-hubbar kan konverteras till säkra virtuella hubbar genom att distribuera Azure-brandväggen i VWAN-hubbar för att aktivera molnbaserad säkerhet, åtkomst och principkontroll. Orkestrering av Azure-brandväggar i virtuella WAN-hubbar kan utföras av Azure Firewall Manager.

[Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) tillhandahåller funktioner för att hantera och skala säkerhet för globala transitnätverk. Azure Firewall Manager ger möjlighet att centralt hantera routning, global principhantering, avancerade Internet-säkerhetstjänster via tredje part tillsammans med Azure-brandväggen.

![säker virtuell hubb med Azure-brandvägg](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Bild 5: Skyddad virtuell hubb med Azure-brandvägg**

Azure-brandväggen till det virtuella WAN-programmet stöder följande globala skyddade transitanslutningssökvägar. Bokstäverna inom parentes mappas till figur 5.

* VNet-till-VNet säker transit (e)
* VNet-till-Internet eller säkerhetstjänst från tredje part (i)
* Säkerhetstjänst från filial till Internet eller säkerhetstjänst från tredje part (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet-till-VNet säker transit (e)

Den skyddade anslutningen för VNet-till-VNet gör det möjligt för virtuella nätverk att ansluta till varandra via Azure-brandväggen i den virtuella WAN-hubben.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-till-Internet eller säkerhetstjänst från tredje part (i)

Den skyddade överföringen från VNet till Internet eller från tredje part gör det möjligt för virtuella nätverk att ansluta till internet eller en säkerhetstjänst från tredje part som stöds via Azure-brandväggen i den virtuella WAN-hubben.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Säkerhetstjänst från filial till Internet eller säkerhetstjänst från tredje part (j)
Den skyddade grenen till Internet eller den säkra transiten från tredje part gör det möjligt för grenar att ansluta till internet eller en säkerhetstjänst från tredje part som stöds via Azure-brandväggen i den virtuella WAN-hubben.

## <a name="next-steps"></a>Nästa steg

Skapa en anslutning med Virtual WAN och Distribuera Azure-brandväggen i VWAN-hubberna.

* [Anslutningar från plats till plats med Virtual WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-anslutningar med Virtual WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager distribuerar Azure FW i VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
