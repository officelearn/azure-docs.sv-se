---
title: inkludera fil
description: inkludera fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187876"
---
Stegen för den här aktiviteten använder ett VNet baserat på värdena i följande konfigurations referens lista. Ytterligare inställningar och namn beskrivs också i den här listan. Vi använder inte den här listan direkt i något av stegen, men vi lägger till variabler baserat på värdena i den här listan. Du kan kopiera listan som ska användas som referens och ersätta värdena med dina egna.

* Virtual Network namn = "TestVNet"
* Virtual Network adress utrymme = 192.168.0.0/16
* Resurs grupp = "TestRG"
* Subnet1 Name = "FrontEnd" 
* Subnet1 Address Space = "192.168.1.0/24"
* Gateway-undernätets namn: "GatewaySubnet" du måste alltid namnge ett Gateway-undernät *GatewaySubnet*.
* Gateway-undernät adress utrymme = "192.168.200.0/26"
* Region = "östra USA"
* Gateway-namn = "GW"
* Gatewayens IP-namn = "GWIP"
* Gateway IP-konfiguration namn = "gwipconf"
* Skriv = "ExpressRoute" den här typen krävs för en ExpressRoute-konfiguration.
* Gateway offentlig IP-namn = "gwpip"

## <a name="add-a-gateway"></a>Lägga till en gateway
1. Anslut till din Azure-prenumeration.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarera variablerna för den här övningen. Se till att redigera exemplet för att avspegla de inställningar som du vill använda.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Lagra det virtuella nätverks objektet som en variabel.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Lägg till ett Gateway-undernät till din Virtual Network. Gateway-undernätet måste ha namnet "GatewaySubnet". Du bör skapa ett Gateway-undernät som är/27 eller större (/26,/25 osv.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ange konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Lagra Gateway-undernätet som en variabel.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Begär en offentlig IP-adress. IP-adressen begärs innan gatewayen skapas. Du kan inte ange den IP-adress som du vill använda. den tilldelas dynamiskt. I nästa konfigurationsavsnitt ska du använda denna IP-adress. AllocationMethod måste vara dynamisk.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Skapa konfigurationen för din gateway. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. I det här steget anger du den konfiguration som ska användas när du skapar gatewayen. Det här steget skapar inte objektet Gateway. Använd exemplet nedan för att skapa din gateway-konfiguration.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Skapa gatewayen. I det här steget är **-GatewayType** särskilt viktigt. Du måste använda värdet **ExpressRoute**. När du har kört dessa cmdletar kan gatewayen ta 45 minuter eller mer att skapa.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Kontrol lera att gatewayen har skapats
Använd följande kommandon för att kontrol lera att gatewayen har skapats:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [Gateway-SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU: n när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-Gateway. Om du vill ändra din gateway till en UltraPerformance-Gateway måste du först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance-Gateway. Om du vill nedgradera din gateway från en UltraPerformance-Gateway måste du först ta bort UltraPerformance-gatewayen och sedan skapa en ny Gateway.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Ta bort en gateway
Använd följande kommando för att ta bort en gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
