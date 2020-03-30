---
title: VPN Gateway Classic till Resource Manager Migration | Microsoft-dokument
description: Den här sidan innehåller en översikt över migrering av VPN Gateway Classic to Resource Manager.
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
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c1a75630c6419816b048495ee87d24c81979af16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064775"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway-klassiker till Resource Manager-migrering
VPN-gateways kan nu migreras från klassisk till Resurshanterarens distributionsmodell. Du kan läsa mer om Azure Resource [Manager-funktioner och fördelar](../azure-resource-manager/management/overview.md). I den här artikeln beskriver vi hur du migrerar från klassiska distributioner till nyare Resource Manager-baserad modell. 

VPN-gateways migreras som en del av VNet-migreringen från klassisk till Resurshanteraren. Den här migreringen görs ett virtuella nätverk i taget. Det finns inget ytterligare krav när det gäller verktyg eller förutsättningar för migrering. Migreringsstegen är identiska med befintlig VNet-migrering och dokumenteras på [IaaS-resursmigreringssida](../virtual-machines/windows/migration-classic-resource-manager-ps.md). Det finns ingen avbrottstid för datasökvägen under migreringen och befintliga arbetsbelastningar skulle därför fortsätta att fungera utan förlust av lokal anslutning under migreringen. Den offentliga IP-adressen som är associerad med VPN-gatewayen ändras inte under migreringsprocessen. Detta innebär att du inte behöver konfigurera om din lokala router när migreringen är klar.  

Modellen i Resource Manager skiljer sig från klassisk modell och består av virtuella nätverksgateways, lokala nätverksgateways och anslutningsresurser. Dessa representerar själva VPN-gatewayen, den lokala platsen som representerar lokalt adressutrymme och anslutning mellan de två. När migreringen är klar är gateways inte tillgängliga i klassisk modell och alla hanteringsåtgärder på virtuella nätverksgateways, lokala nätverksgateways och anslutningsobjekt måste utföras med Hjälp av Resource Manager-modellen.

## <a name="supported-scenarios"></a>Scenarier som stöds
De vanligaste VPN-anslutningsscenarierna omfattas av den klassiska till Resource Manager-migreringen. De scenarier som stöds omfattar -

* Peka på platsanslutning
* Webbplats till platsanslutning med VPN Gateway ansluten till på lokal plats
* VNet till VNet-anslutning mellan två virtuella nätverk med VPN-gateways
* Flera virtuella nätverk som är anslutna till samma plats på lokal plats
* Anslutning på flera webbplatser
* Påtvingad tunnelning aktiverade virtuella nätverk

Scenarier som inte stöds inkluderar -  

* VNet med både ExpressRoute Gateway och VPN Gateway stöds för närvarande inte.
* Transitscenarier där VM-tillägg är anslutna till lokala servrar. Begränsningar för transit-VPN-anslutning beskrivs nedan.

> [!NOTE]
> CIDR-validering i Resource Manager-modellen är striktare än den i klassisk modell. Innan du migrerar se till att klassiska adressintervall som anges överensstämmer med giltigt CIDR-format innan migreringen påbörjas. CIDR kan valideras med hjälp av alla vanliga CIDR-validerare. VNet eller lokala platser med ogiltiga CIDR-intervall när migrerade skulle resultera i misslyckat tillstånd.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet till VNet-anslutningsmigrering
VNet till VNet-anslutning i klassisk uppnåddes genom att skapa en lokal platsrepresentation av det anslutna virtuella nätverket. Kunderna var tvungna att skapa två lokala webbplatser som representerade de två virtuella nätverk som behövde anslutas tillsammans. Dessa kopplades sedan till motsvarande virtuella nätverk med IPsec-tunneln för att upprätta anslutning mellan de två virtuella nätverken. Den här modellen har hanterbarhetsutmaningar eftersom alla adressintervalländringar i ett virtuella nätverk också måste underhållas i motsvarande lokala webbplatsrepresentation. I Resource Manager-modellen behövs inte längre den här lösningen. Anslutningen mellan de två virtuella nätverken kan nås direkt med hjälp av anslutningstypen Vnet2Vnet i anslutningsresursen. 

![Skärmbild av VNet till VNet-migrering.](./media/vpn-gateway-migration/migration1.png)

Under VNet-migrering upptäcker vi att den anslutna entiteten till nuvarande VNets VPN-gateway är ett annat virtuella nätverk och ser till att när migreringen av båda virtuella nätverken är slutförd, skulle du inte längre se två lokala platser som representerar det andra virtuella nätverket. Den klassiska modellen med två VPN-gateways, två lokala platser och två anslutningar mellan dem omvandlas till Resource Manager-modellen med två VPN-gateways och två anslutningar av typen Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Transit VPN-anslutning
Du kan konfigurera VPN-gateways i en topologi så att lokal anslutning för ett virtuella nätverk uppnås genom att ansluta till ett annat virtuella nätverk som är direkt ansluten till lokalt. Detta är transit VPN-anslutning där instanser i första virtuella nätverk är anslutna till lokala resurser via överföring till VPN-gatewayen i ansluten VNet som är direkt ansluten till lokalt. För att uppnå den här konfigurationen i klassisk distributionsmodell måste du skapa en lokal plats som har aggregerade prefix som representerar både det anslutna virtuella nätverket och det lokala adressutrymmet. Den här representationella lokala platsen ansluts sedan till det virtuella nätverket för att uppnå transitanslutning. Den här klassiska modellen har också liknande hanterbarhetsutmaningar eftersom alla ändringar i lokalt adressintervall också måste underhållas på den lokala platsen som representerar summan av virtuella nätverk och lokala. Införandet av BGP-stöd i Resource Manager-gateways som stöds förenklar hanterbarheten eftersom de anslutna gatewayerna kan lära sig vägar från lokalt utan manuell ändring av prefix.

![Skärmbild av transitroutningsscenariot.](./media/vpn-gateway-migration/migration2.png)

Eftersom vi omvandlar VNet till VNet-anslutning utan att det kräver lokala platser, förlorar transitscenariot lokal anslutning för virtuella nätverk som är indirekt ansluten till lokalt. Förlusten av anslutning kan minskas på följande två sätt, efter att migreringen har slutförts - 

* Aktivera BGP på VPN-gateways som är anslutna tillsammans och till lokala. Aktivera BGP återställer anslutningen utan någon annan konfigurationsändring eftersom vägar lärs in och annonseras mellan VNet-gateways. Observera att BGP-alternativet endast är tillgängligt på standard och högre SKU:er.
* Upprätta en explicit anslutning från det berörda virtuella nätverket till den lokala nätverksgatewayen som representerar lokal plats. Detta skulle också kräva att ändra konfigurationen på den lokala routern för att skapa och konfigurera IPsec-tunneln.

## <a name="next-steps"></a>Nästa steg
När du har lärt dig mer om VPN gateway migration support, gå till [plattformsstödd migrering av IaaS-resurser från klassiska till Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-ps.md) för att komma igång.

