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
ms.openlocfilehash: 8803aada61ae58f1e221767aeb382f7d74c63eb4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326531"
---
### <a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

Så här lägger du till ytterligare adressprefix:

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
```

Så här tar du bort adressprefix:<br>
Utelämna de prefix som du inte längre behöver. I det här exemplet vi inte längre prefixet 10.101.2.0/24 (från föregående exempel), så vi uppdatera den lokala nätverksgatewayen utan att prefix.

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
```

### <a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefixet som ingår i din lokala nätverksgateway, behöver du genomföra följande steg i turordning. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefix behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.


1. Ta bort anslutningen.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. Ändra IP-adressprefixen för din lokala nätverksgateway.
   
  Ställ in variabeln för LocalNetworkGateway.

  ```azurepowershell-interactive
  $local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```
   
  Ändra prefixen.
   
  ```azurepowershell-interactive
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```
3. Skapa anslutningen. I det här exemplet konfigurerar vi en IPsec-anslutningstyp. När du återskapar anslutningen kan du använda den anslutningstyp som har angetts för din konfiguration. Ytterligare anslutningar finns på sidan [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx).
   
  Ställ in variabeln för VirtualNetworkGateway.

  ```azurepowershell-interactive
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
  ```
   
  Skapa anslutningen. I det här exemplet används variabeln $local som du angav i steg 2.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1 -Location 'East US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```