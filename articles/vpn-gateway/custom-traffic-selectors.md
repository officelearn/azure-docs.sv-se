---
title: Anpassade trafik väljare för Azure VPN Gateway-anslutningar | Microsoft Docs
description: Lär dig mer om hur du använder anpassade trafik väljare på VPN Gateway anslutningar
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: radwiv
ms.openlocfilehash: cf9401b21d4aa736a6edeae5146d1355f2d49d1d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512163"
---
# <a name="custom-traffic-selectors-for-vpn-gateway-connections"></a>Anpassade trafik väljare för VPN Gateway anslutningar

Du kan ange anpassade trafik väljare på VPN gateway-anslutningarna. Möjligheten att ställa in trafik väljare gör att du kan begränsa adress utrymmen från båda sidor av de VPN-tunnlar som du vill skicka trafik till. Anpassade trafik väljare är särskilt användbara när du har ett stort VNet-adressutrymme, men vill använda ett av dina undernät för IPsec/IKE-förhandling.

Du kan lägga till anpassade trafik väljare när du skapar en ny anslutning, eller så kan du uppdatera den för en befintlig anslutning. Parametern `TrafficSelectorPolicies` består av en matris med trafik väljare. Varje trafik väljare innehåller en samling lokala och fjärranslutna adress intervall i CIDR-format.

## <a name="add-custom-traffic-selectors"></a>Lägg till anpassade trafik väljare

I följande exempel visas hur anpassade trafik väljare kan skapas för en virtuell nätverksgateway-anslutning med PowerShell-kommandon. Information om hur du skapar en anslutning till en virtuell nätverksgateway finns i [Konfigurera en plats-till-plats-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md).

1. Ange värden för parametern *trafficselectorpolicies* i $trafficselectorpolicy. När du ställer in detta ser du att värdet *New-AzTrafficSelectorPolicy* tar lokala och fjärranslutna adress intervall i en matris.

   ```azurepowershell-interactive
   $trafficSelectorPolicy = New-AzTrafficSelectorPolicy `
   -LocalAddressRanges ("10.10.10.0/24", "20.20.20.0/24") `
   -RemoteAddressRanges ("30.30.30.0/24", "40.40.40.0/24")
   ```
2. Skapa en ny anslutning för virtuell nätverksgateway. Justera värde parametrarna för dina behov.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection `
   -ResourceGroupName $rgname `
   -name $vnetConnectionName `
   -location $location `
   -VirtualNetworkGateway1 $vnetGateway `
   -LocalNetworkGateway2 $localnetGateway `
   -ConnectionType IPsec `
   -RoutingWeight 3 `
   -SharedKey $sharedKey `
   -UsePolicyBasedTrafficSelectors $true `
   -TrafficSelectorPolicies ($trafficSelectorPolicy)
   ```

Du kan också uppdatera anpassade trafik väljare för en befintlig anslutning för virtuell nätverksgateway med set-AzVirtualNetworkGatewayConnection. För PowerShell-värden, se [anslutning till virtuell](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?)nätverksgateway.

## <a name="next-steps"></a>Nästa steg

Mer information om VPN-gatewayer finns i [om VPN gateway](vpn-gateway-about-vpngateways.md).