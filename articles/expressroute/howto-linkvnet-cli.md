---
title: "Länka ett virtuellt nätverk till en ExpressRoute-krets: CLI: Azure | Microsoft Docs"
description: "Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (Vnet) för ExpressRoute-kretsar med hjälp av Resource Manager-modellen och CLI."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: anzaman,cherylmc
ms.openlocfilehash: 0ea696e796ec3a943bc028f56da417978b728b82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av CLI

Den här artikeln hjälper dig att länka virtuella nätverk (Vnet) för Azure ExpressRoute-kretsar med hjälp av CLI. Om du vill länka med Azure CLI, måste de virtuella nätverken skapas med Resource Manager-distributionsmodellen. De kan antingen vara i samma prenumeration, eller en del av en annan prenumeration. Om du vill använda en annan metod för att ansluta ditt VNet till en ExpressRoute-krets kan du välja en artikel från följande lista:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Du behöver den senaste versionen av kommandoradsgränssnittet (CLI). Mer information finns i [installera Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
* Du behöver gå igenom den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Du måste ha en aktiv ExpressRoute-krets. 
  * Följ instruktionerna för att [skapar du en ExpressRoute-krets](howto-circuit-cli.md) och ha kretsen aktiveras med anslutningsleverantören. 
  * Se till att du har privat Azure-peering konfigurerats för kretsen. Finns det [konfigurera routning](howto-routing-cli.md) artikel routning anvisningar. 
  * Kontrollera att Azure privat peering är konfigurerad. BGP-peering mellan ditt nätverk och Microsoft måste vara in så att du kan aktivera anslutning för slutpunkt till slutpunkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ instruktionerna för att [konfigurera en virtuell nätverksgateway för ExpressRoute](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Se till att använda `--gateway-type ExpressRoute`.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste vara i samma region geopolitiska när du använder en standard ExpressRoute-krets. 

* Om du aktiverar ExpressRoute premium-tillägg kan du länka ett virtuellt nätverk utanför geopolitiska region för ExpressRoute-kretsen eller ansluta ett stort antal virtuella nätverk till ExpressRoute-kretsen. Mer information om premium-tillägg finns i [vanliga frågor och svar](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk med samma prenumeration till en krets

Du kan ansluta en virtuell nätverksgateway till en ExpressRoute-krets med hjälp av exemplet. Kontrollera att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör kommandot.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets

Du kan dela en ExpressRoute-krets över flera prenumerationer. Bilden nedan visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av mindre molnen i stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Varje avdelning inom organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men de kan dela en enda ExpressRoute-krets att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutning och bandbredd kostnader för dedikerade kretsen tillämpas på ExpressRoute-kretsen ägare. Alla virtuella nätverk dela på samma bandbredd.
> 
> 

![Anslutning över prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Administration - krets ägare och krets användare

Kretsägaren är en auktoriserad Power användare av resursen ExpressRoute-kretsen. Kretsägaren kan skapa tillstånd som kan lösas av krets-användare. Kretsen användare är ägare till virtuella nätverks-gateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Kretsen användare kan lösa tillstånd (en auktorisering per virtuellt nätverk).

Kretsägaren har behörighet att ändra och återkalla tillstånd när som helst. När ett tillstånd återkallas tas alla anslutningar bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Så här skapar du ett tillstånd**

Kretsägaren skapar tillstånd, vilket skapar auktoriseringsnyckel som kan användas av en Kretsanvändare för att ansluta sina virtuella nätverksgatewayerna till ExpressRoute-kretsen. Ett tillstånd som är giltig för endast en anslutning.

I följande exempel visas hur du skapar ett tillstånd:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Svaret innehåller auktoriseringsnyckeln och status:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Granska tillstånd**

Kretsägaren kan granska alla tillstånd som utfärdats för en viss krets genom att köra följande exempel:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Att lägga till tillstånd**

Kretsägaren kan lägga till tillstånd med hjälp av följande exempel:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Ta bort tillstånd**

Kretsägaren kan återkalla/ta bort tillstånd till användaren genom att köra följande exempel:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Kretsen användare

Kretsen användaren måste peer-ID och auktoriseringsnyckel från Kretsägaren. Auktoriseringsnyckeln är ett GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Lösa in en anslutningsverifiering**

Kretsen kan köras i följande exempel för att lösa in en länk-tillstånd:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Att släppa en anslutningsverifiering**

Du kan släppa tillstånd genom att ta bort anslutningen som länkar ExpressRoute-kretsen till det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).