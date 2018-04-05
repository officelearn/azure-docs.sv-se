---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ee182202cf1ecbbb0845541269f7241de26c170
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
### <a name="gwipnoconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning.

När du ändrar det här värdet kan du också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway för att skriva över de aktuella inställningarna. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen befintlig gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Om det redan finns en gatewayanslutning, måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 

1. Ta bort anslutningen. Du kan ta reda på namnet på anslutningen med hjälp av cmdleten Get-AzureRmVirtualNetworkGatewayConnection.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1
  ```
2. Ändra värdet för GatewayIpAddress. Du kan också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 `
  -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
  ```
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPsec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).  För att få VirtualNetworkGateway-namnet kan du köra cmdlet:en Get-AzureRmVirtualNetworkGateway.
   
    Ange variablerna.

  ```azurepowershell-interactive
  $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  ```
   
    Skapa anslutningen.

  ```azurepowershell-interactive 
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
  -Location "East US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```