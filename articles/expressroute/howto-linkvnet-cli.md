---
title: 'Azure ExpressRoute: Länka ett VNet till krets: CLI'
description: Den här artikeln visar hur du länkar virtuella nätverk (VNets) till ExpressRoute-kretsar med hjälp av Resurshanterarens distributionsmodell och CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476142"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med CLI

Den här artikeln hjälper dig att länka virtuella nätverk (VNets) till Azure ExpressRoute-kretsar med CLI. Om du vill länka med Azure CLI måste de virtuella nätverken skapas med hjälp av resurshanterarens distributionsmodell. De kan antingen vara i samma prenumeration eller en del av en annan prenumeration. Om du vill använda en annan metod för att ansluta ditt virtuella nätverk till en ExpressRoute-krets kan du välja en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Du behöver den senaste versionen av kommandoradsgränssnittet (CLI). Mer information finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Du måste granska [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ instruktionerna för att [skapa en ExpressRoute-krets](howto-circuit-cli.md) och ha kretsen aktiverad av din anslutningsleverantör. 
  * Se till att du har Azure privat peering konfigurerad för din krets. Se [konfigurationsroutningsartikeln](howto-routing-cli.md) för routningsinstruktioner. 
  * Kontrollera att Azure-privat peering är konfigurerad. BGP-peering mellan nätverket och Microsoft måste vara uppe så att du kan aktivera end-to-end-anslutning.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapad och fullständigt etablerad. Följ instruktionerna för att [konfigurera en virtuell nätverksgateway för ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Var noga `--gateway-type ExpressRoute`med att använda .

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma geopolitiska region när du använder en standard-ExpressRoute-krets. 

* Ett enda virtuella nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd processen nedan för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Om du aktiverar Premium-tillägget ExpressRoute kan du länka ett virtuellt nätverk utanför den geopolitiska regionen i ExpressRoute-kretsen eller ansluta ett större antal virtuella nätverk till expressroutekretsen. Mer information om premiumtillägget finns i [vanliga frågor](expressroute-faqs.md)och svar .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk i samma prenumeration till en krets

Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av exemplet. Kontrollera att den virtuella nätverksgatewayen skapas och är klar för länkning innan du kör kommandot.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets

Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar en enkel schematisk av hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar inom en organisation. Var och en av avdelningarna inom organisationen kan använda sin egen prenumeration för att distribuera sina tjänster – men de kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings- och bandbreddsavgifter för den dedikerade kretsen kommer att tillämpas på ExpressRoute Circuit Owner. Alla virtuella nätverk har samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - Kretsägare och kretsanvändare

"Kretsägaren" är en auktoriserad Strömanvändare av ExpressRoute-kretsresursen. Kretsägaren kan skapa auktoriseringar som kan lösas in av "Circuit Users". Circuit Users är ägare av virtuella nätverksgateways som inte ligger inom samma prenumeration som ExpressRoute-kretsen. Circuit-användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har befogenhet att ändra och återkalla auktoriseringar när som helst. När en auktorisering återkallas tas alla länkanslutningar bort från prenumerationen vars åtkomst återkallades.

### <a name="circuit-owner-operations"></a>Kretsägare verksamhet

**Så här skapar du en auktorisering**

Kretsägaren skapar en auktorisering, som skapar en auktoriseringsnyckel som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgateways till ExpressRoute-kretsen. En auktorisering gäller endast för en anslutning.

I följande exempel visas hur du skapar en auktorisering:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Svaret innehåller auktoriseringsnyckeln och statusen:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Så här granskar du auktoriseringar**

Kretsägaren kan granska alla auktoriseringar som utfärdas på en viss krets genom att köra följande exempel:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Så här lägger du till auktoriseringar**

Kretsägaren kan lägga till auktoriseringar med hjälp av följande exempel:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Så här tar du bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar till användaren genom att köra följande exempel:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Kretsanvändaråtgärder

Kretsanvändaren behöver peer-ID och en auktoriseringsnyckel från kretsägaren. Auktoriseringsnyckeln är ett GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Så här löser du in en anslutningsauktorisering**

Kretsanvändaren kan köra följande exempel för att lösa in en länkauktorisering:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Så här frigör du ett anslutningsauktorisering**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverksanslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverksanslutning. 

**Så här uppdaterar du anslutningsvikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från mer än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skickas för den här prefixet kan du ändra *Routningsvikt* för en anslutning. Trafiken skickas på anslutningen med den högsta *routningsvikten*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Intervallet *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om din ExpressRoute-krets finns på [ExpressRoute Direct](expressroute-erdirect-about.md) och din virtuella newtork-gateway är Ultra Performance eller ErGw3AZ. FastPath förbättrar förformancen för datasökväg, till exempel paket per sekund och anslutningar per sekund mellan det lokala nätverket och det virtuella nätverket. 

**Konfigurera FastPath på en ny anslutning**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Uppdatera en befintlig anslutning för att aktivera FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
