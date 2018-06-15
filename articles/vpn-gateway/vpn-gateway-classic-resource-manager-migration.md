---
title: VPN Gateway Classic till Resource Manager migreringen | Microsoft Docs
description: Den här sidan innehåller en översikt över den VPN-Gateway klassiska till hanteraren för filserverresurser.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23885219"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN-Gateway classic till Resource Manager-migreringen
VPN-gatewayer kan nu migreras från classic till Resource Manager-distributionsmodellen. Du kan läsa mer om Azure Resource Manager [funktioner och fördelar](../azure-resource-manager/resource-group-overview.md). I den här artikeln får information om hur du migrerar från klassiska distributioner till nyare Resource Manager-baserat modellen. 

VPN-gatewayer migreras som en del av VNet migrering från klassisk till Resource Manager. Migreringen är klar ett virtuellt nätverk i taget. Det finns inga ytterligare krav termer verktyg eller krav för migrering. Migreringssteg är identiska med befintliga VNet migrering och dokumenteras i [IaaS resurser migrering sida](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Det finns inga data sökväg avbrottstid vid migrering och därmed befintliga arbetsbelastningar kan fortsätta att fungera utan lokal anslutningsproblem under migreringen. Den offentliga IP-adressen som är associerad med VPN-gatewayen ändras inte under migreringsprocessen. Detta innebär att du inte behöver konfigurera om den lokala routern när migreringen har slutförts.  

Modellen i Resource Manager skiljer sig från den klassiska modellen och består av virtuella nätverks-gateway, lokala nätverksgatewayer och resurser för anslutningen. Dessa motsvarar VPN-gatewayen, den lokala platsen som representerar på lokala adressutrymmet och anslutningen mellan två respektive. När migreringen är klar din gateway är inte tillgängliga i den klassiska modellen och alla hanteringsåtgärder på gateway för virtuellt nätverk och lokala nätverksgatewayer anslutningsobjekt måste utföras med hjälp av Resource Manager-modellen.

## <a name="supported-scenarios"></a>Scenarier som stöds
Vanliga scenarier för VPN-anslutningen omfattas av classic till Resource Manager-migreringen. Scenarierna som stöds omfattar-

* Peka på plats-anslutning
* Plats till plats-anslutning med VPN-Gateway som är ansluten till på den lokala platsen
* VNet till VNet-anslutning mellan två Vnet med hjälp av VPN-gatewayer
* Flera virtuella nätverk som är anslutna till samma i lokal plats
* Flera plats-anslutning
* Tvingad tunneling aktiverat Vnet

Scenarier som inte stöds är-  

* Virtuella nätverk med både ExpressRoute-Gateway och VPN-Gateway stöds inte för närvarande.
* Överföring scenarier där VM-tillägg som är anslutna till lokala servrar. Begränsningar för överföring VPN-anslutningen beskrivs nedan.

> [!NOTE]
> CIDR-verifiering i Resource Manager-modellen är striktare än i den klassiska modellen. Innan du migrerar du säkerställa att klassiska adressintervall anges överensstämmer med giltigt CIDR-format innan du påbörjar migreringen. CIDR kan verifieras med hjälp av några vanliga CIDR verifierare. Virtuellt nätverk eller lokala platser med ogiltig CIDR-intervallen när migreras skulle resultera i felaktigt tillstånd.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Virtuella nätverk för migrering av virtuella nätverk anslutning
VNet till VNet-anslutningar i klassiskt uppnåddes genom att skapa en lokal plats representation av anslutna virtuella nätverk. Kunder var tvungen att skapa två lokala webbplatser som representerade två Vnet som behövs för att anslutas tillsammans. Dessa var sedan ansluten till motsvarande Vnet med hjälp av IPsec-tunneln för att upprätta en anslutning mellan två Vnet. Den här modellen har hanterbarhet utmaningar eftersom adressintervallet ändringar i ett virtuellt nätverk måste även hanteras i den motsvarande lokala plats-representationen. Den här lösningen behövs inte längre i Resource Manager-modellen. Anslutningen mellan två Vnet kan uppnås direkt med anslutningstypen 'Vnet2Vnet' i anslutningen resurs. 

![Skärmbild av VNet för migrering av virtuella nätverk.](./media/vpn-gateway-migration/migration1.png)

Under migreringen av virtuella nätverk känner vi av att den anslutna enheten till aktuella VNet VPN-gateway är ett annat VNet och se till att du, när migreringen av båda Vnet har slutförts, inte längre skulle se två lokala webbplatser som representerar den andra VNet. Den klassiska modellen av två VPN-gatewayer, två lokala webbplatser och två anslutningar mellan dem omvandlas till Resource Manager-modellen med två VPN-gatewayer och två anslutningar av typen Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>VPN-anslutning för överföring
Du kan konfigurera VPN-gatewayer i en topologi som lokal anslutning för ett virtuellt nätverk uppnås genom att ansluta till ett annat virtuellt nätverk som är direkt ansluten till lokalt. Detta är överföring VPN-anslutning där instanser i första virtuella nätverk är anslutna till lokala resurser via överföringen till VPN-gateway i anslutna virtuella nätverk som är direkt ansluten till lokalt. För att uppnå den här konfigurationen i klassiska distributionsmodellen, måste du skapa en lokal plats som har samman prefix som representerar anslutna VNet och lokala adressutrymmet. Representational lokala platsen ansluts sedan till VNet att uppnå anslutning för överföring. Den här klassiska modellen har också liknande hanterbarhet utmaningar eftersom ändringar i lokala adressintervallet måste även hanteras på den lokala platsen som representerar summan av VNet och lokalt. Introduktion av BGP-stöd i Resource Manager stöds gateways underlättas hanterbarhet anslutna gateway kan lära sig vägarna från lokalt utan manuella ändringar på prefix.

![Skärmbild av överföring routningsscenario.](./media/vpn-gateway-migration/migration2.png)

Eftersom vi transformera VNet till VNet-anslutningen utan lokala platser förlorar överföring scenariot lokalt anslutningen för virtuella nätverk som indirekt är ansluten till lokalt. Förlust av anslutning kan begränsas på följande två sätt när migreringen är klar- 

* Aktivera BGP på VPN-gatewayer som ansluts till varandra och till lokalt. Aktivera BGP återställer anslutning utan någon konfigurationsändring eftersom vägar lärt dig och annonserade mellan VNet-gateway. Observera att BGP-alternativet är bara tillgängligt på Standard och högre SKU: er.
* Skapa en explicit anslutning från berörda VNet till den lokala nätverksgateway som representerar lokal plats. Detta kräver också ändra konfigurationen på den lokala routern att skapa och konfigurera IPsec-tunneln.

## <a name="next-steps"></a>Nästa steg
När du lära dig mer om stöd för VPN-gateway-migrering, gå till [plattform som stöds migrering av IaaS-resurser från klassisk till Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) att komma igång.

