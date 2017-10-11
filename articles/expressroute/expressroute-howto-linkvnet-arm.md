---
title: "Länka ett virtuellt nätverk till en ExpressRoute-krets: PowerShell: Azure | Microsoft Docs"
description: "Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (Vnet) för ExpressRoute-kretsar med Resource Manager-distributionsmodellen och PowerShell."
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: ganesr
ms.openlocfilehash: 8c2f3036f754a98090ab860f95900416690ebf83
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln hjälper dig att länka virtuella nätverk (Vnet) Azure ExpressRoute-kretsar med hjälp av Resource Manager-distributionsmodellen och PowerShell. Virtuella nätverk kan antingen vara i samma prenumeration eller en del av en annan prenumeration. Den här artikeln beskriver också hur du uppdaterar en länk för virtuellt nätverk. 

## <a name="before-you-begin"></a>Innan du börjar
* Installera den senaste versionen av Azure PowerShell-moduler. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Granska de [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ instruktionerna för att [skapar du en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och ha kretsen aktiveras med anslutningsleverantören. 
  * Se till att du har privat Azure-peering konfigurerats för kretsen. Finns det [konfigurera routning](expressroute-howto-routing-arm.md) artikel routning anvisningar. 
  * Kontrollera att privat Azure-peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ instruktionerna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway expressroute använder GatewayType ExpressRoute, inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste vara i samma region geopolitiska när du använder en standard ExpressRoute-krets. 

* Du kan länka en virtuella nätverk utanför geopolitiska region för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-krets om du har aktiverat ExpressRoute premium-tillägg. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägg.


## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk med samma prenumeration till en krets
Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av mindre molnen i stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Varje avdelning inom organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Prenumerationsägaren kommer att gälla anslutningar och bandbredd avgifter för ExpressRoute-kretsen. Alla virtuella nätverk dela på samma bandbredd.
> 
> 

![Anslutning över prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - krets ägare och krets användare

Kretsägaren är en auktoriserad Power användare av resursen ExpressRoute-kretsen. Kretsägaren kan skapa tillstånd som kan lösas av krets-användare. Kretsen användare är ägare till virtuella nätverks-gateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsen användare kan lösa tillstånd (en auktorisering per virtuellt nätverk).

Kretsägaren har behörighet att ändra och återkalla tillstånd när som helst. Återkalla ett tillstånd som resulterar i alla anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Så här skapar du ett tillstånd**

Kretsägaren skapar tillstånd. Detta resulterar i att skapa auktoriseringsnyckel som kan användas av en kretsanvändare för att ansluta sina virtuella nätverksgatewayerna till ExpressRoute-kretsen. Ett tillstånd som är giltig för endast en anslutning.

Följande cmdlet utdrag visar hur du skapar ett tillstånd:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Auktoriseringsnyckeln och status innehåller svar på detta:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Granska tillstånd**

Kretsägaren kan granska alla tillstånd som utfärdats för en viss krets genom att köra följande cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Att lägga till tillstånd**

Kretsägaren kan lägga till tillstånd genom att använda följande cmdlet:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Ta bort tillstånd**

Kretsägaren kan återkalla/ta bort tillstånd till användaren genom att köra följande cmdlet:

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Kretsen användaren åtgärder

Kretsen användaren måste peer-ID och auktoriseringsnyckel från kretsägaren. Auktoriseringsnyckeln är ett GUID.

Peer-ID kan kontrolleras från följande kommando:

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Lösa in en anslutningsverifiering**

Kretsanvändare kan köra följande cmdlet för att lösa in en länk-tillstånd:

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Att släppa en anslutningsverifiering**

Du kan släppa tillstånd genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="modify-a-virtual-network-connection"></a>Ändra en virtuell nätverksanslutning
Du kan uppdatera vissa egenskaper för virtuella nätverk. 

**Uppdatera anslutning vikt**

Det virtuella nätverket kan vara ansluten till flera ExpressRoute-kretsar. Du kan få samma prefix från mer än en ExpressRoute-kretsen. Du kan ändra för att välja vilken anslutning du vill skicka trafik till det här prefixet *RoutingWeight* för en anslutning. Trafik skickas på anslutning med den högsta *RoutingWeight*.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Intervallet för *RoutingWeight* är 0 till 32000. Standardvärdet är 0.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
