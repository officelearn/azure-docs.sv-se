---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187876"
---
Stegen för den här uppgiften använder ett virtuella nätverk baserat på värdena i följande konfigurationsreferenslista. Ytterligare inställningar och namn beskrivs också i den här listan. Vi använder inte den här listan direkt i något av stegen, även om vi lägger till variabler baserat på värdena i den här listan. Du kan kopiera listan som ska användas som referens och ersätta värdena med dina egna.

* Namn på virtuellt nätverk = "TestVNet"
* Virtuellt nätverksadressutrymme = 192.168.0.0/16
* Resursgrupp = "TestRG"
* Undernät1 Namn = "FrontEnd" 
* Undernät1 adressutrymme = "192.168.1.0/24"
* Gateway Undernät namn: "GatewaySubnet" Du måste alltid namnge en gateway undernät *GatewaySubnet*.
* Undernätsutrymme för gateway = "192.168.200.0/26"
* Region = "Östra USA"
* Gateway Namn = "GW"
* Gateway IP-namn = "GWIP"
* Gateway IP-konfiguration Namn = "gwipconf"
* Type = "ExpressRoute" Den här typen krävs för en ExpressRoute-konfiguration.
* Gateway Public IP-namn = "gwpip"

## <a name="add-a-gateway"></a>Lägga till en gateway
1. Anslut till din Azure-prenumeration.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarera variablerna för den här övningen. Var noga med att redigera exemplet så att det återspeglar de inställningar som du vill använda.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Lagra det virtuella nätverksobjektet som en variabel.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Lägg till ett gateway-undernät i det virtuella nätverket. Gateway-undernätet måste heta "GatewaySubnet". Du bör skapa ett gateway-undernät som är /27 eller större (/26, /25 osv.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ange konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Lagra gateway-undernätet som en variabel.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Begär en offentlig IP-adress. IP-adressen begärs innan gatewayen skapas. Du kan inte ange den IP-adress som du vill använda. den är dynamiskt fördelad. I nästa konfigurationsavsnitt ska du använda denna IP-adress. AllocationMethod måste vara dynamisk.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Skapa konfigurationen för din gateway. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. I det här steget anger du den konfiguration som ska användas när du skapar gatewayen. Det här steget skapar faktiskt inte gateway-objektet. Använd exemplet nedan för att skapa din gateway-konfiguration.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Skapa gatewayen. I det här steget är **-GatewayType** särskilt viktigt. Du måste använda värdet **ExpressRoute**. När du har kört dessa cmdlets kan gatewayen ta 45 minuter eller mer att skapa.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Verifiera att gatewayen har skapats
Använd följande kommandon för att kontrollera att gatewayen har skapats:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [SKU:er för gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan när som helst använda följande kommando för att ändra gateway-SKU: n.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance gateway. Om du vill ändra din gateway till en UltraPerformance-gateway tar du först bort den befintliga ExpressRoute-gatewayen och skapar sedan en ny UltraPerformance-gateway. Om du vill nedgradera gatewayen från en UltraPerformance-gateway tar du först bort UltraPerformance-gatewayen och skapar sedan en ny gateway.
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
