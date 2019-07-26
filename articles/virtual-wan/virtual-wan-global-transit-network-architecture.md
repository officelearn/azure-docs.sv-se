---
title: Azure Virtual WAN global-överföring av nätverks arkitektur | Microsoft Docs
description: Lär dig mer om global transit nätverks arkitektur för virtuellt WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 2376c77ecc328788c842e045aafb618cbad39b0e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421426"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Global överföring av nätverks arkitektur och virtuellt WAN

Global transit nätverks arkitektur antas av företag för att konsolidera, ansluta och kontrol lera det molnbaserade moderna företags IT-företaget. I ett modernt, molnbaserad företags nätverk behöver inte nätverks trafik vara pådragna till HQ. Global överförings nätverks arkitektur baseras på både välkända nätverks koncept och nya koncept som är unika för moln-och molnbaserade arkitekturer.

![Arkitektur](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Bild 1: Globalt överförings nätverk med virtuellt WAN**

Moderna företag kräver allmänt förekommande-anslutning mellan Hyper-distribuerade program, data och användare i hela molnet och lokalt. Azure Virtual WAN möjliggör en global överförings nätverks arkitektur genom att aktivera allmänt förekommande, alla anslutningar mellan globalt distribuerade uppsättningar av virtuella nätverk, webbplatser, program och användare. Azure Virtual WAN är en Microsoft-hanterad tjänst. Alla nätverks komponenter som den här tjänsten består av är värdbaserade och hanteras av Microsoft. Mer information om virtuellt WAN finns i artikeln [Översikt över virtuella WAN-nätverk](virtual-wan-about.md) .

I Azure Virtual WAN-arkitekturen fungerar Azure-regioner som hubbar som du kan välja att ansluta dina grenar till. När grenarna är anslutna kan du utnyttja Azure-stamnätet för att upprätta gren-till-VNet-anslutningar och, om du vill, gren-till-gren-anslutning.

Du kan upprätta ett virtuellt WAN-nätverk genom att skapa en enda virtuell WAN-hubb i den region som har det största antalet ekrar (grenar, virtuella nätverk, användare) och sedan ansluta de ekrar som finns i andra regioner till hubben. Alternativt, om ekrar är geografiskt distribuerade, kan du även instansiera regionala hubbar och koppla hubbarna. Hubbarna är alla delar av samma virtuella WAN-nätverk, men de kan kopplas till olika regionala principer.

## <a name="hub"></a>NAV-och-eker-överföring

Den globala överförings nätverks arkitekturen baseras på en klassisk nav-och-eker-nätverksanslutning där det molnbaserade nätverkets hubb möjliggör transitiv anslutning mellan slut punkter som kan fördelas mellan olika typer av ekrar.
  
I den här modellen kan ekrar vara:

* Virtuellt nätverk (virtuella nätverk)
* Fysisk gren plats
* Fjärranvändare
* Internet

![globalt överförings diagram för nav och eker](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Bild 2: Hubb och eker**

Bild 2 visar den logiska vyn av det globala nätverket där geografiskt distribuerade användare, fysiska platser och virtuella nätverk är sammankopplade via en nätverks hubb som finns i molnet. Den här arkitekturen möjliggör logisk överföring av ett hopp mellan nätverks slut punkterna. Ekrarna är anslutna till hubben av olika Azure-nätverkstjänster som ExpressRoute eller plats-till-plats-VPN för fysiska grenar, VNet-peering för virtuella nätverk och punkt-till-plats-VPN för fjärran vändare.

## <a name="crossregion"></a>Anslutning mellan regioner

För ett företag följer en moln storlek normalt den fysiska storleken. De flesta företag får åtkomst till molnet från en region som är närmast deras fysiska plats och användare. En av huvud kontona för global nätverks arkitektur är att aktivera anslutning mellan olika regioner mellan nätverks enheter och slut punkter. En moln storlek kan sträcka sig över flera regioner. Det innebär att trafik från en gren som är ansluten till molnet i en region kan komma åt en annan gren eller ett VNet i en annan region med nav-till-hubb-anslutning, som för närvarande finns i vår översikt.

## <a name="any"></a>Alla-till-alla-anslutningar

Global överförings nätverks arkitektur möjliggör *alla-till-alla anslutningar* via en central nätverks hubb. Den här arkitekturen eliminerar eller minskar behovet av att använda kompletta nät eller partiella nät anslutnings modeller som är mer komplexa för att bygga och underhålla. Dessutom är routnings kontroll i nav-och-eker vs. nät nätverk enklare att konfigurera och underhålla.

Alla-till-alla-anslutningar, i kontexten för en global arkitektur, gör det möjligt för företag med globalt distribuerade användare, grenar, data Center, virtuella nätverk och program att ansluta till varandra via överförings navet. Överförings navet fungerar som globalt system för överföring.

![trafik Sök vägar](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Bild 3: Vägar för virtuella WAN-trafik**

Azure Virtual WAN stöder följande anslutnings vägar för global överföring. Bokstäverna i parenteser mappas till bild 3.

* Branch-till-VNet (a)  
* Gren-till-gren (b)
* Fjärran vändare till VNet (c)
* Fjärran sluten användare-till-gren (d)
* VNet-till-VNet med VNet-peering (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Branch-till-VNet

Branch-till-VNet är den primära sökvägen som stöds av Azure Virtual WAN. Med den här sökvägen kan du ansluta grenar till Azure IAAS Enterprise-arbetsbelastningar som har distribuerats i Azure virtuella nätverk. Grenar kan anslutas till det virtuella WAN-nätverket via ExpressRoute eller VPN för plats till plats. Trafik överföringarna till virtuella nätverk som är anslutna till de virtuella WAN-hubbarna via VNet-anslutningar.

### <a name="branchbranch"></a>Gren-till-gren

Grenar kan anslutas till ett virtuellt Azure-nav med ExpressRoute-kretsar och/eller plats-till-plats-VPN-anslutningar. Du kan ansluta grenar till det virtuella WAN-hubben i den region som ligger närmast grenen.

Med det här alternativet kan företag utnyttja Azures stamnät för att ansluta grenar. Men även om den här funktionen är tillgänglig bör du väga fördelarna med att ansluta grenar över Azure Virtual WAN jämfört med ett privat WAN.

### <a name="usertovnet"></a>Fjärran sluten användare-till-VNet

Du kan aktivera direkt säker fjärråtkomst till Azure med punkt-till-plats-anslutningar från en fjärran sluten användar klient till ett virtuellt WAN. Företags-fjärran vändare behöver inte längre hairpin till molnet med hjälp av ett företags-VPN.

### <a name="usertobranch"></a>Fjärran sluten användare-till-gren

Med sökvägen för fjärran sluten användare kan fjärran vändare som använder en punkt-till-plats-anslutning till Azure komma åt lokala arbets belastningar och program genom att använda molnet. Den här sökvägen ger fjärran vändare möjlighet att komma åt arbets belastningar som båda är distribuerade i Azure och lokalt. Företag kan aktivera central molnbaserad säker fjärråtkomst i Azure Virtual WAN.

### <a name="vnetvnet"></a>VNet-till-VNet-överföring med VNet-peering

Använd VNet-peering för att ansluta virtuella nätverk till varandra för att stödja program med flera nivåer som implementeras i flera virtuella nätverk. Ett VNet-till-VNet-överförings scenario via Azure Virtual WAN stöds inte för närvarande, men det finns i Azure-översikten. Anslutning av virtuella nätverk via VNet-peering är den rekommenderade lösningen för virtuella nätverk som måste vara anslutna till varandra. [Gateway-överföring](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (i kontexten för VNet-peering) krävs inte för virtuellt WAN-nätverk eftersom virtuella WAN automatiskt aktiverar Gateway-överföring.

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute är ett privat och flexibelt sätt att ansluta dina lokala nätverk till Microsoft Cloud. ExpressRoute Global Reach är en tilläggs funktion för ExpressRoute. Med Global Reach kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. Grenar som är anslutna till Azure Virtual WAN med ExpressRoute kräver att ExpressRoute-Global Reach kommunicerar med varandra.

I den här modellen kan varje gren som är ansluten till den virtuella WAN-hubben med ExpressRoute ansluta till virtuella nätverk med hjälp av gren-till-VNet-sökvägen. Gren-till-gren-trafik översätter inte navet eftersom ExpressRoute Global Reach ger en mer optimal sökväg över Azure WAN.

## <a name="security"></a>Säkerhet och princip kontroll

Det virtuella nätverkets hubb samansluter och kan se all överförings trafik. Det kan vara den plats som fungerar som värd för centrala nätverksfunktioner och tjänster, till exempel en moln cirkulation, nätverks policy och säkerhet samt Internet åtkomst kontroll.

## <a name="next-steps"></a>Nästa steg

Skapa en anslutning med Virtual WAN.

* [Plats-till-plats-anslutningar med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-anslutningar med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md)
