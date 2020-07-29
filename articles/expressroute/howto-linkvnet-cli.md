---
title: 'Azure-ExpressRoute: länka ett virtuellt nätverk till en krets: CLI'
description: Den här artikeln visar hur du länkar virtuella nätverk (virtuella nätverk) till ExpressRoute-kretsar med hjälp av distributions modellen och CLI för Resource Manager.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: cherylmc
ms.openlocfilehash: 3d51409baf9d5ca6ce90f1367ac128d7d47a9757
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325261"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med CLI

Den här artikeln hjälper dig att länka virtuella nätverk (virtuella nätverk) till Azure ExpressRoute-kretsar med CLI. Om du vill länka med Azure CLI måste de virtuella nätverken skapas med distributions modellen för Resource Manager. De kan antingen finnas i samma prenumeration eller ingå i en annan prenumeration. Om du vill använda en annan metod för att ansluta ditt VNet till en ExpressRoute-krets kan du välja en artikel från följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Du behöver den senaste versionen av kommando rads gränssnittet (CLI). Mer information finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Du måste granska kraven, [prerequisites](expressroute-prerequisites.md) [routningsstatus](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](howto-circuit-cli.md) och ha den krets som Aktiver ATS av anslutnings leverantören. 
  * Se till att du har konfigurerat Azures privata peering för din krets. Mer information finns i artikeln [om konfigurering av routning](howto-routing-cli.md) . 
  * Se till att Azures privata peering har kon figurer ATS. BGP-peering mellan ditt nätverk och Microsoft måste vara upp så att du kan aktivera slutpunkt-till-slutpunkt-anslutningen.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [Konfigurera en virtuell nätverksgateway för ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Var noga med att använda `--gateway-type ExpressRoute` .

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region när man använder en standard ExpressRoute-krets. 

* Ett enda VNet kan länkas till upp till fyra ExpressRoute-kretsar. Använd processen nedan för att skapa ett nytt anslutnings objekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Om du aktiverar ExpressRoute Premium-tillägget kan du länka ett virtuellt nätverk utanför den nationella politisk regionen för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-kretsen. Mer information om Premium-tillägget finns i [vanliga frågor och svar](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets

Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av exemplet. Kontrol lera att den virtuella Nätverksgatewayen har skapats och är redo för länkning innan du kör kommandot.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets

Du kan dela en ExpressRoute-krets över flera prenumerationer. Figuren nedan visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation. Var och en av avdelningarna i organisationen kan använda sin egen prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. Alla virtuella nätverk delar samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration – krets ägare och krets användare

Krets ägaren är en behörig privilegie rad användare av ExpressRoute-krets resursen. Krets ägaren kan skapa auktoriseringar som kan lösas av "krets användare". Krets användare är ägare till virtuella nätverksgateway som inte ingår i samma prenumeration som ExpressRoute-kretsen. Krets användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. När en auktorisering återkallas tas alla länk anslutningar bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Så här skapar du en auktorisering**

Krets ägaren skapar en auktorisering, som skapar en auktoriseringsregel som kan användas av en krets användare för att ansluta sina virtuella nätverksgateway till ExpressRoute-kretsen. En auktorisering är endast giltig för en anslutning.

I följande exempel visas hur du skapar en auktorisering:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Svaret innehåller verifierings nyckel och status:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Granska auktoriseringar**

Krets ägaren kan granska alla auktoriseringar som utfärdats på en viss krets genom att köra följande exempel:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Lägga till auktoriseringar**

Krets ägaren kan lägga till auktoriseringar med hjälp av följande exempel:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Ta bort auktoriseringar**

Krets ägaren kan återkalla/ta bort auktoriseringarna till användaren genom att köra följande exempel:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Krets användar åtgärder

Krets användaren behöver peer-ID och en nyckel för autentisering från krets ägaren. Auktoriseringsregeln är ett GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Så här löser du en auktorisering av anslutningen**

Krets användaren kan köra följande exempel för att lösa in en länk auktorisering:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Så här släpper du en auktorisering för anslutning**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverks anslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverks anslutning. 

**Så här uppdaterar du anslutnings vikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från fler än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skicka trafik till det här prefixet kan du ändra *RoutingWeight* för en anslutning. Trafiken kommer att skickas till anslutningen med den högsta *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute-FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om din virtuella nätverksgateway är Ultra Performance eller ErGw3AZ. FastPath förbättrar data Sök vägs förformingen, till exempel paket per sekund och anslutningar per sekund mellan ditt lokala nätverk och ditt virtuella nätverk. 

**Konfigurera FastPath på en ny anslutning**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Uppdatera en befintlig anslutning för att aktivera FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
