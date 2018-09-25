---
title: Om zonredundant virtuella nätverksgatewayer i Tillgänglighetszoner i Azure | Microsoft Docs
description: Läs mer om VPN-Gateway och ExpressRoute-gatewayer i Tillgänglighetszoner.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 27bac5265a5e884b808c4ccb58fda0b2fffeb774
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975671"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Om zonredundant virtuella nätverksgatewayer i Tillgänglighetszoner i Azure

Du kan distribuera VPN och ExpressRoute-gatewayer i [Azure Availability Zones](../availability-zones/az-overview.md). Detta ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribuera gateways i Azure Availability Zones fysisk och logiskt separerar gatewayer inom en region, samtidigt som du skyddar din lokal nätverksanslutning till Azure från zonen på servernivå fel.

### <a name="zrgw"></a>Zonredundant-gatewayer

För att automatiskt distribuera din virtuella nätverksgatewayer mellan tillgänglighetszoner, kan du använda zonredundant virtuella nätverksgatewayer. Med zonredundant gatewayer, kan du dra nytta av zonelasticitet åtkomst till dina verksamhetskritiska, skalbara tjänster på Azure.

<br>
<br>

![zon redunant gatewayer bild](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonindelade gateways

Du kan använda zonindelad gatewayer för att distribuera gateways i en viss zon. När du distribuerar en zonindelad gateway distribueras alla instanser av en gateway i samma Tillgänglighetszon.

<br>
<br>

![zonindelade gatewayer bild](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Gateway-SKU:er

Zonredundant och zonindelade gatewayer är tillgängliga som ny gateway SKU: er. Vi har lagt till nya virtuella nätverkets gateway SKU: er i Azure-AZ regioner. Dessa SKU: er liknar motsvarande befintliga SKU: er för ExpressRoute och VPN-Gateway, förutom att de är specifika för zonredundant och zonindelade gatewayer.

Den nya gatewayen SKU: er är:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Offentliga IP-SKU: er

Zonredundant gateway och zonindelade gateway förlitar sig på Azure offentlig IP-adressresurs *Standard* SKU. Konfigurationen av Azure offentlig IP-adressresurs avgör om den gateway som du distribuerar är zonredundant, eller zonindelade. Om du skapar en offentlig IP-resurs med en *grundläggande* SKU, gatewayen har inte någon redundans och gateway-resurserna blir regionala.

### <a name="pipzrg"></a>Zonredundant-gatewayer

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU utan att ange en zon, beteendet skiljer sig beroende på om gatewayen är en VPN-gateway eller en ExpressRoute-gateway. 

* Två gateway-instanserna ska distribueras i 2 utanför dessa tre zoner att tillhandahålla redundans för en VPN-gateway. 
* För en ExpressRoute-gateway, eftersom det kan finnas fler än två instanser kan gatewayen omfatta flera alla tre zoner.

### <a name="pipzg"></a>Zonindelade gateways

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU och ange zonen (1, 2 eller 3), alla gatewayinstanser ska distribueras i samma zon.

### <a name="piprg"></a>Regionala gateways

När du skapar en offentlig IP-adress med hjälp av den **grundläggande** offentliga IP-SKU gatewayen distribueras som en regional gateway och inte har någon zonredundans som är inbyggda i gatewayen.

## <a name="faq"></a>Vanliga frågor och svar

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad ändras när jag distribuerar de här nya SKU: er?

Du kan distribuera dina gatewayer med redundans från ditt perspektiv. Det innebär att alla instanser av gateway som ska distribueras till Azure Availability Zones och varje Tillgänglighetszon är en annan fel- och domän. Detta gör dina gatewayer mer tillförlitlig, tillgängliga och elastiska zon fel.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure-portalen?

Ja, du kan använda Azure-portalen för att distribuera de nya SKU: er. Dock visas dessa nya SKU: er endast i de Azure-regioner som har Azure Availability Zones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU: er?

De nya SKU: er är tillgängliga i Azure-regioner som har Azure Availability Zones – USA, centrala, Frankrike, centrala och Europa, västra. Framöver kommer tillgängliggör vi Zonredundant gateway till dig i andra offentliga Azure-regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera min befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad-gatewayer?

Migrera dina befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad gatewayer stöds för närvarande inte. Du kan dock ta bort den befintliga gatewayen och skapa en zonredundant eller zonindelad gateway igen.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera VPN- och Expressroute-gatewayer i samma virtuella nätverk?

Samtidig både VPN och Expressroute-gatewayer i samma virtuella nätverk stöds. Du bör dock reservera en/27 IP-adressintervall för gateway-undernätet.

## <a name="next-steps"></a>Nästa steg

[Skapa en zonredundant virtuell nätverksgateway](create-zone-redundant-vnet-gateway.md)