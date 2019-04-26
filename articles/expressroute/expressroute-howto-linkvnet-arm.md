---
title: 'Länka ett virtuellt nätverk till en ExpressRoute-krets: PowerShell: Azure | Microsoft Docs'
description: Det här dokumentet innehåller en översikt över hur du länka virtuella nätverk (Vnet) till ExpressRoute-kretsar med hjälp av Resource Manager-distributionsmodellen och PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/30/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: b0ae3d5b2c60badfb9b59820b1e0b85383e524ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60366356"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln hjälper dig att länka virtuella nätverk (Vnet) till Azure ExpressRoute-kretsar med hjälp av Resource Manager-distributionsmodellen och PowerShell. Virtuella nätverk kan antingen vara i samma prenumeration eller en del av en annan prenumeration. Den här artikeln visar också hur du uppdaterar en länk för virtuellt nätverk.

* Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets som standard. Alla virtuella nätverken måste finnas i samma geopolitiska region när du använder en standard ExpressRoute-krets. 

* Ett enskilt virtuellt nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd stegen i den här artikeln för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka virtuella nätverk utanför den geopolitiska regionen för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-krets om du har aktiverat ExpressRoute premium-tillägget. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägget.


## <a name="before-you-begin"></a>Innan du börjar

* Granska den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och aktivera kretsen av anslutningsprovidern. 
  * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se den [konfigurera routning](expressroute-howto-routing-arm.md) artikeln routning anvisningar. 
  * Se till att Azures privata peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Kontrollera att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [skapar en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType ExpressRoute, inte VPN.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets
Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av följande cmdlet. Se till att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör cmdlet: en:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre moln inom det stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Var och en av avdelningarna i organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Avgifter för anslutning och bandbredd för ExpressRoute-krets används på prenumeranten. Alla virtuella nätverk delar samma bandbredden.
> 
> 

![Anslutningen mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - krets ägare och kretsanvändare

Kretsen ägare är en behörig Power-användare i ExpressRoute-krets resursen. Kretsägaren kan skapa auktoriseringar som kan lösas genom att ”kretsanvändare”. Kretsanvändare är ägare till virtuella nätverksgatewayer som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsanvändare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har rätt att ändra och återkalla auktoriseringar när som helst. Återkallar ett auktorisering resulterar i alla anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Skapa en auktorisering**

Kretsägaren skapar en auktorisering. Detta resulterar i att skapa en auktoriseringsnyckeln som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgatewayer för ExpressRoute-kretsen. En auktorisering är giltig för endast en anslutning.

Följande cmdlet-kodfragment visar hur du skapar en auktorisering:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Svaret på detta innehåller auktoriseringsnyckeln och status:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Granska auktoriseringar**

Kretsägaren kan granska alla auktoriseringar som utfärdas på en viss krets genom att köra följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Att lägga till auktoriseringar**

Kretsägaren kan lägga till tillstånd med hjälp av följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Att ta bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar för användaren genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Kretsen användaråtgärder

Kretsen användaren måste peer-ID och en auktoriseringsnyckeln från kretsägaren. Auktoriseringsnyckeln är ett GUID.

Peer-ID kan kontrolleras från följande kommando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Att kunna lösa in en anslutningsauktorisering**

Kretsanvändare kan köra följande cmdlet för att lösa in en länk-auktorisering:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Att släppa en anslutningsauktorisering**

Du kan släppa en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverksanslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverksanslutning. 

**Uppdatera anslutningsvikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från mer än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skicka trafik till det här prefixet, du kan ändra *RoutingWeight* för en anslutning. Trafik skickas på anslutningen med den högsta *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
