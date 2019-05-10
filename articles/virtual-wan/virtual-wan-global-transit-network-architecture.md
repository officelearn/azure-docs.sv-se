---
title: Azure virtuellt WAN-nätverk globala överföring nätverksarkitektur | Microsoft Docs
description: Läs mer om global överföring nätverksarkitektur för virtuellt WAN-nätverk
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/06/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 8cda617ca60a17fceaaa818480ff9bbaef46c3fd
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414057"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Global överföring nätverksarkitektur och virtuellt WAN-nätverk

Global överföring nätverksarkitektur antas av företag att konsolidera, Anslut och styr molnet-centric moderna företag IT-fotavtrycket. I en modern cloud-centric företag behöver nätverkstrafik inte vara backhauled till HQ. Global överföring nätverksarkitektur baseras på både välbekanta nätverksrelaterade begrepp och nya begrepp som är unika för moln- och molnbaserade arkitekturer.

![Arkitektur](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Bild 1: Global övergångsnätverket med virtuellt WAN-nätverk**

Moderna företag kräver allt vanligare anslutning mellan hyper distribuerade program, data och användare både i molnet och lokalt. Azure virtuellt WAN-nätverk kan en global överföring nätverksarkitektur genom att aktivera allt vanligare, alla-till-alla anslutningar mellan globalt distribuerade uppsättningar av virtuella nätverk, webbplatser, program och användare. Azure virtuellt WAN-nätverk är en Microsoft-hanterad tjänst. Alla nätverkskomponenter som den här tjänsten består av hyst och hanterad av Microsoft. Mer information om virtuellt WAN-nätverk finns i den [virtuella WAN-översikt](virtual-wan-about.md) artikeln.

Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar i Azure virtuella WAN-arkitekturen. När grenarna är ansluten, kan du utnyttja Azures stamnät för att upprätta gren-till-VNet och eventuellt gren till grenen anslutning.

Du kan skapa ett virtuellt WAN-nätverk genom att skapa en enda virtuella WAN-hubb i den region som har det största antalet ekrar (grenar, virtuella nätverk, användare) och sedan ansluta ekrarna som finns i andra regioner till hubben. Om ekrar är utspridda geografiskt, kan du också skapa en instans av regionala hubbar och sammankoppling i hubs. Hubbarna alla som ingår i samma virtuella WAN, men de kan vara associerade med olika regionala principer.

## <a name="hub"></a>NAV-och-eker-överföring

Nätverksarkitektur globala överföringen baseras på en modell för klassiska NAV och eker anslutning där molnvärd nätverket ”hub' möjliggör transitiva anslutningar mellan slutpunkter som kan distribueras över olika typer av 'ekrar'.
  
I den här modellen kan en eker vara:

* Virtuellt nätverk (Vnet)
* Fysiska gren plats
* Fjärranvändare
* Internet

![NAV och ekrar globala överföring diagram](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Bild 2: Hub-and-spoke**

Bild 2 visar logisk vy för globala nätverk där geografiskt distribuerade användare, fysiska platser och virtuella nätverk är sammankopplade via nätverk hub finns i molnet. Den här arkitekturen möjliggör logiska ett hopp överföring anslutning mellan slutpunkter i nätverk. Ekrarna är anslutna till hubben med olika Azure nätverkstjänster, till exempel ExpressRoute eller plats-till plats-VPN för fysiska grenar, VNet-peering för virtuella nätverk och punkt-till-plats-VPN för fjärranslutna användare.

## <a name="crossregion"></a>Anslutningen mellan regioner

För ett företag följer en molntjänster vanligtvis fysiska storleken. De flesta företag åtkomst till molnet från en region som är närmast deras fysiska plats och användare. En av viktiga huvudkonton av globala nätverksarkitektur är att aktivera interregionala anslutning mellan nätverksentiteter och slutpunkter. En molntjänster kan sträcka sig över flera regioner. Det innebär att trafik från en gren som är anslutna till molnet i en region kan nå en annan gren eller ett virtuellt nätverk i en annan region.

## <a name="any"></a>Alla-till-alla anslutningar

Gör att globala överföring nätverksarkitektur *alla-till-alla anslutningar* via hub centrala nätverket. Den här arkitekturen eliminerar eller minskar behovet av helt nät eller partiella nät anslutningsmodeller som är mer komplexa för att bygga och underhålla. Dessutom är Routning kontroll i NAV och eker kontra nät nätverk enklare att konfigurera och underhålla.

Alla-till-alla anslutningar kan i samband med en global arkitektur företag med globalt distribuerade användare, grenar, Datacenter, virtuella nätverk och program för att ansluta till varandra via överföring hub. Överföring navet fungerar som globala överföring.

![trafiksökvägar](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Bild 3: Virtuella WAN-trafiksökvägar**

Azure virtuellt WAN-nätverk stöder följande globala överföring anslutning sökvägar. Bokstäver inom parentes mappar till bild 3.

* Branch-till-VNet (a)  
* Branch-gren (b)
* Fjärranslutna användare-till-VNet (c)
* Användare-till-fjärrgren (d)
* Med hjälp av VNet peering (e) VNet-till-VNet
* ExpressRoute Global räckvidd 

### <a name="branchvnet"></a>Branch-to-VNet

Branch-till-VNet är den primära sökvägen som stöds av Azure virtuellt WAN-nätverk. Den här sökvägen kan du ansluta grenar till Azure IAAS-företagsarbetsbelastningar som har distribuerats i virtuella Azure-nätverk. Grenar kan anslutas till den virtuella WAN-nätverk via ExpressRoute eller VPN för plats-till-plats. Eltransit för trafik till virtuella nätverk som är anslutna till de virtuella WAN hubs via VNet-anslutningar.

### <a name="branchbranch"></a>Branch-to-branch

Grenar kan anslutas till en Azure virtuellt WAN-hubb med ExpressRoute-kretsar och/eller plats-till-plats VPN-anslutningar. Du kan ansluta grenarna till den virtuella WAN-hubb som är i regionen närmast grenen.

Det här alternativet kan företag utnyttja Azures stamnät för att ansluta grenar. Även om den här funktionen är tillgänglig, måste du väga fördelarna med att ansluta grenar via Azure virtuellt WAN-nätverk och att använda ett privat WAN.

### <a name="usertovnet"></a>Fjärranslutna användare-till-VNet

Du kan aktivera direkt, säker fjärråtkomst till Azure med hjälp av punkt-till-plats-anslutningar från en fjärranvändare klient till ett virtuellt WAN. Enterprise fjärranslutna användare behöver inte längre hairpin till molnet med en företagets VPN-anslutning.

### <a name="usertobranch"></a>Fjärranslutna användare att avdelningskontor

Fjärranslutna användare att avdelningskontor sökvägen kan fjärranslutna användare som använder en punkt-till-plats-anslutning till Azure åtkomst lokala arbetsbelastningar och program genom att transporteras via molnet. Den här sökvägen kan fjärranslutna användare för åtkomst-arbetsbelastningar som är både distribuerade i Azure och lokalt. Företag kan aktivera centrala molnbaserade säker fjärråtkomst-tjänsten i Azure virtuellt WAN-nätverk.

### <a name="vnetvnet"></a>VNet-till-VNet-överföring med VNet-peering

Anslut virtuella nätverk till varandra för att stödja flernivåprogram som implementeras över flera virtuella nätverk genom att använda VNet-peering. Ett scenario för överföring av VNet-till-VNet via Azure virtuellt WAN-nätverk stöds inte för närvarande, men är på Azure-översikten. Ansluta virtuella nätverk via VNet-Peering är rekommenderad lösning för virtuella nätverk som måste vara anslutna till varandra. Läs mer om VNet-peering, [VNet Peering översikt](../virtual-network/virtual-network-peering-overview.md).

### <a name="globalreach"></a>ExpressRoute Global räckvidd

ExpressRoute är en privat och flexibel sätt att ansluta ditt lokala nätverk till Microsoft Cloud. Global räckvidd i ExpressRoute är en tilläggsfunktion för ExpressRoute. Du kan länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk med Global räckvidd. Grenar som är anslutna till Azure virtuellt WAN-nätverk med hjälp av ExpressRoute kräver den ExpressRoute Global räckvidd att kommunicera med varandra.

Varje gren som är ansluten till virtuella WAN-hubb med ExpressRoute kan ansluta till virtuella nätverk med hjälp av gren-till-VNet-sökvägen i den här modellen. Gren till grenen trafik överföra inte hubben eftersom ExpressRoute Global räckvidd tillåter en mer optimala sökväg via WAN till Azure.

## <a name="security"></a>Kontroll av säkerhet och principer

Virtuellt nätverk hubben sammanbinder och ser potentiellt alla överföringstrafik. Det kan vara lämpligt att värden centrala nätverk funktioner och tjänster, till exempel sådana en cloud routning, nätverkspolicy och säkerhet och åtkomstkontroll för Internet.

## <a name="next-steps"></a>Nästa steg

Skapa en anslutning med hjälp av virtuellt WAN-nätverk.

* [Plats-till-plats-anslutningar som använder virtuellt WAN-nätverk](virtual-wan-site-to-site-portal.md)
* [Punkt-till-plats-anslutningar som använder virtuellt WAN-nätverk](virtual-wan-point-to-site-portal.md)
* [ExpressRoute-anslutningar med hjälp av virtuellt WAN-nätverk](virtual-wan-expressroute-portal.md)
