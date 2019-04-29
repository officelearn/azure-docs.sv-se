---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419165"
---
### <a name="gwipnoconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning.

När du ändrar det här värdet kan du också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway för att skriva över de aktuella inställningarna. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen befintlig gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Om det redan finns en gatewayanslutning, måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 

1. Ta bort anslutningen. Du hittar namnet på din anslutning med hjälp av cmdleten ”Get-AzVirtualNetworkGatewayConnection”.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Ändra värdet för GatewayIpAddress. Du kan också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPsec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).  Du kan köra cmdleten ”Get-AzVirtualNetworkGateway” för att få VirtualNetworkGateway-namnet.
   
    Ange variablerna.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Skapa anslutningen.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```