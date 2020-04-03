---
title: 'ExpressRoute: Länka ett virtuella nätverk till en krets: Azure PowerShell'
description: Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (VNets) till ExpressRoute-kretsar med hjälp av Resurshanterarens distributionsmodell och PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 755b1898ee4cbc32de3a65a6bbc368ecf3eb3acf
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616370"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Koppla ett virtuellt nätverk till en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln hjälper dig att länka virtuella nätverk (VNets) till Azure ExpressRoute-kretsar med hjälp av Resource Manager-distributionsmodellen och PowerShell. Virtuella nätverk kan antingen finnas i samma prenumeration eller en del av en annan prenumeration. Den här artikeln visar också hur du uppdaterar en virtuell nätverkslänk.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma geopolitiska region när du använder en standard-ExpressRoute-krets. 

* Ett enda virtuella nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd stegen i den här artikeln för att skapa ett nytt anslutningsobjekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Du kan länka virtuella nätverk utanför den geopolitiska regionen i ExpressRoute-kretsen eller ansluta ett större antal virtuella nätverk till din ExpressRoute-krets om du har aktiverat Premium-tillägget ExpressRoute. Mer information om premiumtillägget finns i [vanliga frågor](expressroute-faqs.md) och svar.


## <a name="before-you-begin"></a>Innan du börjar

* Granska [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ instruktionerna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och ha kretsen aktiverad av din anslutningsleverantör. 
  * Se till att du har Azure privat peering konfigurerad för din krets. Se [konfigurationsroutningsartikeln](expressroute-howto-routing-arm.md) för routningsinstruktioner. 
  * Kontrollera att Azure-privat peering är konfigurerad och bgp-peering mellan nätverket och Microsoft är uppe så att du kan aktivera end-to-end-anslutning.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapad och fullständigt etablerad. Följ instruktionerna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType 'ExpressRoute', inte VPN.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk i samma prenumeration till en krets
Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den virtuella nätverksgatewayen skapas och är klar för länkning innan du kör cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel schematisk av hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar inom en organisation. Var och en av avdelningarna inom organisationen kan använda sin egen prenumeration för att distribuera sina tjänster – men de kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings- och bandbreddsavgifter för ExpressRoute-kretsen tillämpas på prenumerationsägaren. Alla virtuella nätverk har samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - kretsägare och kretsanvändare

"Kretsägaren" är en auktoriserad Power User av ExpressRoute-kretsresursen. Kretsägaren kan skapa auktoriseringar som kan lösas in av "kretsanvändare". Kretsanvändare är ägare av virtuella nätverksgateways som inte ligger inom samma prenumeration som ExpressRoute-kretsen. Kretsanvändare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Kretsägaren har befogenhet att ändra och återkalla auktoriseringar när som helst. Om du återkallar en auktorisering tas alla länkanslutningar bort från prenumerationen vars åtkomst återkallades.

### <a name="circuit-owner-operations"></a>Kretsägare verksamhet

**Så här skapar du en auktorisering**

Kretsägaren skapar en auktorisering. Detta resulterar i skapandet av en auktoriseringsnyckel som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgateways till ExpressRoute-kretsen. En auktorisering gäller endast för en anslutning.

Följande cmdlet-kodavsnitt visar hur du skapar en auktorisering:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Svaret på detta kommer att innehålla auktoriseringsnyckeln och statusen:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Så här granskar du auktoriseringar**

Kretsägaren kan granska alla tillstånd som utfärdas på en viss krets genom att köra följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Så här lägger du till auktoriseringar**

Kretsägaren kan lägga till auktoriseringar med hjälp av följande cmdlet:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Så här tar du bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar till användaren genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Kretsanvändaråtgärder

Kretsanvändaren behöver peer-ID och en auktoriseringsnyckel från kretsägaren. Auktoriseringsnyckeln är ett GUID.

Peer-ID kan kontrolleras från följande kommando:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Så här löser du in en anslutningsauktorisering**

Kretsanvändaren kan köra följande cmdlet för att lösa in en länkauktorisering:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Så här frigör du ett anslutningsauktorisering**

Du kan frigöra en auktorisering genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverksanslutning
Du kan uppdatera vissa egenskaper för en virtuell nätverksanslutning. 

**Så här uppdaterar du anslutningsvikten**

Ditt virtuella nätverk kan anslutas till flera ExpressRoute-kretsar. Du kan få samma prefix från mer än en ExpressRoute-krets. Om du vill välja vilken anslutning som ska skickas för den här prefixet kan du ändra *Routningsvikt* för en anslutning. Trafiken skickas på anslutningen med den högsta *routningsvikten*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Intervallet *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurera ExpressRoute FastPath 
Du kan aktivera [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) om din virtuella nätverksgateway är Ultra Performance eller ErGw3AZ. FastPath förbättrar datasökvägsprestanda, till exempel paket per sekund och anslutningar per sekund mellan det lokala nätverket och det virtuella nätverket. 

**Konfigurera FastPath på en ny anslutning**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Uppdatera en befintlig anslutning för att aktivera FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
