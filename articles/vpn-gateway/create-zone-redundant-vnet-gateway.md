---
title: Skapa en zonredundant virtuell nätverksgateway i Azure Availability Zones | Microsoft Docs
description: Distribuera VPN-Gateway och ExpressRoute-gatewayer i Tillgänglighetszoner
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: af72b0255c8e01398048f075134efb452f28b81e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417575"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Skapa en zonredundant virtuell nätverksgateway i Azure Availability Zones

Du kan distribuera VPN och ExpressRoute-gatewayer i Tillgänglighetszoner i Azure. Detta ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribuera gateways i Azure Availability Zones fysisk och logiskt separerar gatewayer inom en region, samtidigt som du skyddar din lokal nätverksanslutning till Azure från zonen på servernivå fel. Mer information finns i [om zonredundant virtuella nätverksgatewayer](about-zone-redundant-vnet-gateways.md) och [om Azure Availability Zones](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda antingen PowerShell installerat lokalt på datorn eller Azure Cloud Shell. Om du väljer att installera och använda PowerShell lokalt kräver den senaste versionen av PowerShell-modulen i den här funktionen.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Att använda PowerShell lokalt

Om du använder PowerShell lokalt på datorn i stället för att använda Cloud Shell måste du installera PowerShell-modulen 1.0.0 eller högre. Om du vill kontrollera vilken version av PowerShell som du har installerat, använder du följande kommando:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Deklarera dina variabler

Värden som används för exempelbeskrivningar listas nedan. Vissa av exemplen använder även deklarerade variabler i stegen. Om du använder de här stegen i din egen miljö måste du ersätta värdena med dina egna. När du anger plats, kan du kontrollera att den region som du anger stöds. Mer information finns i den [vanliga frågor och svar](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. Skapa det virtuella nätverket

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Skapa ett virtuellt nätverk.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Lägg till gateway-undernätet

Gateway-undernätet innehåller reserverade IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Använd följande exempel för att lägga till och ange ett gateway-undernät:

Lägg till gateway-undernätet.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ange konfigurationen för gateway-undernät för det virtuella nätverket.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Begär en offentlig IP-adress
 
I det här steget, väljer du de anvisningar som gäller för gateway som du vill skapa. Valet av zoner för att distribuera gateway beror på de zoner som angetts för den offentliga IP-adressen.

### <a name="ipzoneredundant"></a>För zonredundant gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU och inte anger någon zon. I det här fallet blir Standard offentliga IP-adressen skapas en zonredundant offentlig IP-adress.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>För zonindelade gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU. Ange zonen (1, 2 eller 3). Alla gatewayinstanser ska distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>För regionala gateways

Begär en offentlig IP-adress med en **grundläggande** PublicIpaddress SKU. I det här fallet gatewayen distribueras som en regional gateway och har inte någon zonredundans som är inbyggda i gatewayen. Gateway-instanser skapas respektive i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Skapa IP-konfigurationen

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Skapa gatewayen

Skapa den virtuella nätverksgatewayen.

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>För VPN-Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Vanliga frågor och svar

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad ändras när jag distribuerar de här nya SKU: er?

Du kan distribuera dina gatewayer med redundans från ditt perspektiv. Det innebär att alla instanser av gateway som ska distribueras till Azure Availability Zones och varje Tillgänglighetszon är en annan fel- och domän. Detta gör dina gatewayer mer tillförlitlig, tillgängliga och elastiska zon fel.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure-portalen?

Ja, du kan använda Azure-portalen för att distribuera de nya SKU: er. Dock visas dessa nya SKU: er endast i de Azure-regioner som har Azure Availability Zones.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU: er?

De nya SKU: er är tillgängliga i Azure-regioner som har Azure Availability Zones – USA, centrala, Frankrike, centrala, Europa, norra, Europa, västra och USA, västra 2 regioner. Framöver kommer tillgängliggör vi zonredundant gatewayer till dig i andra offentliga Azure-regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera min befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad-gatewayer?

Migrera dina befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad gatewayer stöds för närvarande inte. Du kan dock ta bort den befintliga gatewayen och skapa en zonredundant eller zonindelad gateway igen.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera VPN- och Expressroute-gatewayer i samma virtuella nätverk?

Samtidig både VPN och Expressroute-gatewayer i samma virtuella nätverk stöds. Du bör dock reservera en/27 IP-adressintervall för gateway-undernätet.
