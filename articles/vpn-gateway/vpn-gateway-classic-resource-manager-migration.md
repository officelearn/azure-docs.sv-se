---
title: VPN-Gateway migrering från klassisk till Resource Manager | Microsoft Docs
description: Den här sidan innehåller en översikt över den VPN-Gateway klassiska till Resource Manager-migrering.
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
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761598"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN-Gateway migrering från klassisk till Resource Manager
VPN-gatewayer kan nu migreras från klassisk till Resource Manager-distributionsmodellen. Du kan läsa mer om Azure Resource Manager [funktioner och fördelar](../azure-resource-manager/resource-group-overview.md). I den här artikeln beskriver vi hur du migrerar från klassiska distributioner till den nya Resource Manager-baserade modellen. 

VPN-gatewayer migreras som en del av VNet-migrering från klassisk till Resource Manager. Den här migreringen görs ett virtuellt nätverk i taget. Det finns inga ytterligare krav när det gäller verktyg eller förutsättningar för migrering. Migreringssteg är identiska för migrering av befintliga virtuella nätverket och dokumenteras på [migrering av IaaS resurssidan](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Det finns inga data sökväg driftstopp under migreringen och därmed befintliga arbetsbelastningar skulle fortsätta att fungera utan förlust av lokala anslutningar under migreringen. Offentliga IP-adress som är associerade med VPN-gatewayen ändras inte under migreringsprocessen. Detta innebär att du inte behöver konfigurera om den lokala routern när migreringen är klar.  

Modellen i Resource Manager skiljer sig från den klassiska modellen och består av virtuella nätverksgatewayer, lokala nätverksgatewayer och anslutningsresurser. Dessa motsvarar VPN-gatewayen, den lokala platsen som representerar på lokala adressutrymmen och anslutning mellan två respektive. När migreringen är klar din gateway är inte tillgängliga i den klassiska modellen och alla hanteringsåtgärder på den virtuella nätverksgatewayer, lokala nätverksgatewayer och anslutningsobjekt måste utföras med hjälp av Resource Manager-modellen.

## <a name="supported-scenarios"></a>Scenarier som stöds
De vanligaste scenarier för VPN-anslutning omfattas av klassisk till Resource Manager-migrering. Scenarierna som stöds omfattar-

* Peka på plats-anslutning
* Plats till plats-anslutning med VPN-gatewayen är ansluten till på lokal plats
* VNet-till-VNet-anslutning mellan två virtuella nätverk med hjälp av VPN-gatewayer
* Flera virtuella nätverk som är anslutna till samma på lokal plats
* Flera plats-anslutning
* Tvingad tunneltrafik är aktiverat virtuella nätverk

Scenarier som inte stöds är-  

* Virtuellt nätverk med både ExpressRoute-gatewayen och VPN-Gateway stöds inte för närvarande.
* Överföring scenarier där VM-tillägg är anslutna till den lokala servrar. Överföring VPN-anslutning begränsningar beskrivs nedan.

> [!NOTE]
> CIDR-verifiering i Resource Manager-modellen är striktare än en i klassiska modellen. Innan du migrerar du säkerställa att klassiska adressintervall beroende överensstämmer med giltigt CIDR-format innan du påbörjar migreringen. CIDR kan verifieras med hjälp av några vanliga CIDR-systemhälsoverifierare. Virtuellt nätverk eller lokala platser med ogiltig CIDR-intervall när migreras skulle resultera i ett feltillstånd.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet-till-VNet-anslutning migrering
Anslutningar mellan virtuella nätverk i klassiskt läge uppnåddes genom att skapa en lokal plats-representation av det anslutna virtuella nätverket. Kunder var tvungna att skapa två lokala platser som representeras av de två virtuella nätverken som behövs för att vara är anslutna till varandra. Dessa var sedan anslutna till den motsvarande virtuella nätverk med IPsec-tunneln för att upprätta en anslutning mellan de två virtuella nätverken. Den här modellen har hanterbarhet utmaningar eftersom ändringar adressintervallet i ett virtuellt nätverk måste också finnas kvar i motsvarande lokala platsen representation. Den här lösningen behövs inte längre i Resource Manager-modellen. Anslutningen mellan de två virtuella nätverken kan ske direkt med 'Vnet2Vnet'-anslutningstypen i Connection-resursen. 

![Skärmbild av VNet till VNet-migrering.](./media/vpn-gateway-migration/migration1.png)

Vid migrering av virtuellt nätverk identifierar vi att den anslutna enheten till aktuella VNet VPN-gateway är ett annat virtuellt nätverk och kontrollera att du, när migreringen av båda de virtuella nätverken är klar kan inte längre skulle se två lokala webbplatser som representerar det andra virtuella nätverket. Den klassiska modellen av två VPN-gatewayer, två lokala webbplatser och två anslutningar mellan dem omvandlas till Resource Manager-modellen med två VPN-gatewayer och två anslutningar av typen Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>VPN-anslutning för överföring
Du kan konfigurera VPN-gatewayer i en topologi som lokal anslutning för ett virtuellt nätverk uppnås genom att ansluta till ett annat virtuellt nätverk som är direkt ansluten till den lokala. Det här är överföring VPN-anslutning där instanser i det första virtuella nätverket är anslutna till lokala resurser via överföring till VPN-gateway i anslutna virtuella nätverk som är direkt ansluten till den lokala. För att uppnå den här konfigurationen klassisk distributionsmodell, skulle du behöva skapa en lokal plats som har aggregeras prefix som representerar både det anslutna virtuella nätverket och lokala adressutrymme. Den här representational lokala platsen är sedan ansluten till det virtuella nätverket kan uppnå överföring anslutning. Den här klassiska modellen har också liknande hanterbarhet utmaningar eftersom ändringar i lokala adressintervallet måste också finnas kvar på den lokala platsen som representerar summan av virtuellt nätverk och lokala. Introduktionen av BGP-stöd i Resource Manager stöds gatewayer förenklar hantering eftersom anslutna gateway kan läsa vägar från lokalt utan manuella ändringar på prefix.

![Skärmbild av Routningsscenario för överföring.](./media/vpn-gateway-migration/migration2.png)

Eftersom vi transformera VNet till VNet-anslutning utan att kräva lokala platser, förlorar överföring scenariot lokal anslutning för virtuellt nätverk som är indirekt anslutna till den lokala. Förlust av anslutning kan undvikas på följande två sätt när migreringen är klar- 

* Aktivera BGP på VPN-gatewayer som är anslutna tillsammans och till den lokala. Aktiverar BGP återställer anslutning utan några andra konfigurationsändring eftersom vägar lärt dig och annonseras mellan VNet-gatewayer. Observera att BGP-alternativet är bara tillgängligt för Standard- och högre SKU: er.
* Skapa en explicit anslutning från berörda virtuella nätverket till den lokala nätverksgateway som representerar den lokala platsen. Detta skulle innebära att ändra konfigurationen på den lokala routern att skapa och konfigurera IPsec-tunneln.

## <a name="next-steps"></a>Nästa steg
När du läst om stöd för migrering av VPN-gateway, gå till [plattformsunderstödd migrering av IaaS-resurser från klassisk till Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) att komma igång.

