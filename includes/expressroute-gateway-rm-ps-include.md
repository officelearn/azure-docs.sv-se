---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 17edbef03f1e2882bd85f5a58e2a32a1541b50c8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Stegen för den här aktiviteten använder ett VNet baserat på värdena i följande konfiguration är en lista. Ytterligare inställningar och namn också beskrivs i den här listan. Vi använder inte den här listan i steg, även om vi lägger till variabler baserat på värdena i den här listan. Du kan kopiera listan om du vill använda som en referens, där du ersätter värdena med dina egna.

**Referens för konfigurationslistan**

* Virtuella nätverksnamnet = ”TestVNet”
* Virtuella nätverks-adressutrymme = 192.168.0.0/16
* Resursgruppens namn = ”TestRG”
* Undernät1 Name = ”FrontEnd” 
* Undernät1 adressutrymme = ”192.168.1.0/24”
* Namnet för gateway-undernätet: ”GatewaySubnet” måste du alltid namnge ett gateway-undernät *GatewaySubnet*.
* Gateway-undernätsadressutrymmet = ”192.168.200.0/26”
* Region = ”östra USA”
* Gatewaynamnet = ”GW”
* Gateway IP-Name = ”GWIP”
* Gateway-IP-konfiguration namn = ”gwipconf”
* Typ = ”ExpressRoute” den här typen krävs för en ExpressRoute-konfiguration.
* Gateway offentliga IP-namn = ”gwpip”

## <a name="add-a-gateway"></a>Lägga till en gateway
1. Ansluta till din Azure-prenumeration.

  ```powershell 
  Login-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Deklarera variablerna för den här övningen. Se till att redigera exemplet för att återspegla de inställningar som du vill använda.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Lagra det virtuella nätverksobjektet som en variabel.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Lägg till ett gateway-undernät i det virtuella nätverket. Gateway-undernätet måste ha namnet ”GatewaySubnet”. Du bör skapa en gateway-undernät som är minst/27 eller större (/ 26/25, etc.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Ange konfigurationen.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Lagra gateway-undernätet som en variabel.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Begär en offentlig IP-adress. IP-adress krävs innan du skapar gatewayen. Du kan inte ange IP-adressen som du vill använda. Det allokeras dynamiskt. I nästa konfigurationsavsnitt ska du använda denna IP-adress. AllocationMethod måste vara dynamiska.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Skapa konfigurationen för din gateway. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. I det här steget anger du den konfiguration som ska användas när du skapar gatewayen. Det här steget skapar faktiskt inte gateway-objektet. Använd exemplet nedan för att skapa din gateway-konfiguration.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Skapa en gateway. I det här steget i **- GatewayType** är särskilt viktigt. Du måste använda värdet **ExpressRoute**. När du har kört dessa cmdlets, kan du ta 45 minuter eller mer att skapa gatewayen.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Kontrollera gatewayen som har skapats
Kontrollera att gatewayen har skapats med hjälp av följande kommandon:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [Gateway-SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance gateway. Om du vill ändra din gateway till en gateway för UltraPerformance först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance gateway. Om du vill konvertera din gateway från en UltraPerformance gateway tar du bort UltraPerformance gateway och sedan skapa en ny gateway.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Ta bort en gateway
Använd följande kommando för att ta bort en gateway:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```