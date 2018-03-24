---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d6c23bc7bf9b3228f851ab38ec587bc8552455
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
### <a name="gwipnoconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning.

När du ändrar det här värdet kan du också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway för att skriva över de aktuella inställningarna. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a> Ändra ”GatewayIpAddress” för den lokala nätverksgatewayen – ingen befintlig gatewayanslutning

Om VPN-enheten som du vill ansluta till har bytt offentlig IP-adress måste du ändra den lokala nätverksgatewayen så att den återspeglar den ändringen. Om det redan finns en gatewayanslutning, måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 

1. Ta bort anslutningen. Du kan ta reda på namnet på anslutningen med hjälp av cmdleten Get-AzureRmVirtualNetworkGatewayConnection.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Ändra värdet för GatewayIpAddress. Du kan också ändra adressprefixen på samma gång. Se till att använda det befintliga namnet på din lokala nätverksgateway. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPsec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).  För att få VirtualNetworkGateway-namnet kan du köra cmdlet:en Get-AzureRmVirtualNetworkGateway.
   
    Ange variablerna.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Skapa anslutningen.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```