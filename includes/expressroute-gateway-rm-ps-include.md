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
ms.openlocfilehash: 03a56951b68163a9160cc4a57f15354b5f210eb7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125301"
---
Stegen för den här uppgiften använder ett virtuellt nätverk baserat på värdena i följande konfiguration är en lista. Ytterligare inställningar och namn beskrivs också i den här listan. Vi använda inte den här listan direkt i något av stegen, även om vi lägger till variabler baserat på värdena i den här listan. Du kan kopiera listan om du vill använda som en referens, där du ersätter värdena med dina egna.

* Namn på virtuellt nätverk = ”TestVNet”
* Virtuella nätverkets adressutrymme = 192.168.0.0/16
* Resursgrupp = ”TestRG”
* Subnet1 Name = ”FrontEnd” 
* Subnet1 adressutrymme = ”192.168.1.0/24”
* Namn på gateway-undernät: Du måste alltid namnger gateway-undernätet ”GatewaySubnet” *GatewaySubnet*.
* Adressutrymme för gateway-undernätet = ”192.168.200.0/26”
* Region = ”USA, östra”
* Gateway Name = "GW"
* Gateway IP Name = "GWIP"
* IP-gatewaykonfiguration Name = ”gwipconf”
* Typ = ”ExpressRoute” den här typen krävs för en ExpressRoute-konfigurationen.
* Gateway offentliga IP-namn = ”gwpip”

## <a name="add-a-gateway"></a>Lägga till en gateway
1. Anslut till din Azure-prenumeration.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Deklarera dina variabler för den här övningen. Glöm inte att redigera exemplet för att återspegla de inställningar som du vill använda.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Store det virtuella nätverksobjektet som en variabel.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Lägga till ett gateway-undernät i det virtuella nätverket. Gateway-undernätet måste ha namnet ”GatewaySubnet”. Du bör skapa ett gateway-undernät som är/27 eller större (/ 26, / 25 osv.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ange konfigurationen.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Store gateway-undernätet som en variabel.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Begär en offentlig IP-adress. IP-adress krävs innan du skapar gatewayen. Du kan inte ange IP-adressen som du vill använda. den allokeras dynamiskt. I nästa konfigurationsavsnitt ska du använda denna IP-adress. AllocationMethod måste vara dynamisk.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Skapa konfigurationen för din gateway. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. I det här steget anger du den konfiguration som ska användas när du skapar gatewayen. Det här steget skapar inte gateway-objekt. Använd exemplet nedan för att skapa din gateway-konfiguration.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Skapa gatewayen. I det här steget i **- GatewayType** är särskilt viktigt. Du måste använda värdet **ExpressRoute**. När du har kört dessa cmdletar kan du ta 45 minuter eller mer att skapa gatewayen.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Kontrollera att gatewayen har skapats
Använd följande kommandon för att kontrollera att gatewayen har skapats:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway
Det finns ett antal [Gateway SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra den Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-gateway. Om du vill ändra din gateway till en UltraPerformance-gateway, först ta bort den befintliga ExpressRoute-gatewayen och skapa sedan en ny UltraPerformance-gateway. Ta först bort UltraPerformance-gateway för att nedgradera din gateway från en UltraPerformance-gateway och sedan skapa en ny gateway.
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
