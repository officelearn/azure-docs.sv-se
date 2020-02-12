---
title: Skapa en zon – redundant virtuell nätverksgateway i Azure-tillgänglighetszoner
description: Distribuera VPN Gateway-och ExpressRoute-gatewayer i Tillgänglighetszoner
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/26/2019
ms.author: cherylmc
ms.openlocfilehash: 58e9b4204e2d563d8e4e1af8353870880f98b065
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133601"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Skapa en zon – redundant virtuell nätverksgateway i Azure-tillgänglighetszoner

Du kan distribuera VPN-och ExpressRoute-gatewayer i Azure-tillgänglighetszoner. Detta ger återhämtning, skalbarhet och högre tillgänglighet till virtuella nätverks-gatewayer. Att distribuera gateways i Azure-tillgänglighetszoner fysiskt och logiskt särskiljer gatewayer inom en region, samtidigt som du skyddar din lokala nätverks anslutning till Azure från felaktiga zon nivåer. Mer information finns i [om zoner-redundanta virtuella nätverksgateway](about-zone-redundant-vnet-gateways.md) och [om Azure-tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan antingen använda PowerShell installerat lokalt på datorn eller Azure Cloud Shell. Om du väljer att installera och använda PowerShell lokalt kräver den här funktionen den senaste versionen av PowerShell-modulen.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Använda PowerShell lokalt

Om du använder PowerShell lokalt på din dator, i stället för att använda Cloud Shell, måste du installera PowerShell-modulen 1.0.0 eller högre. Använd följande kommando för att kontrol lera vilken version av PowerShell som du har installerat:

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="variables"></a>1. deklarera dina variabler

Värdena som används för exempel stegen visas nedan. Dessutom använder vissa av exemplen deklarerade variabler i stegen. Om du använder de här stegen i din egen miljö måste du ersätta värdena med dina egna. När du anger plats kontrollerar du att den region som du anger stöds. Mer information finns i [vanliga frågor och svar](#faq).

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

## <a name="configure"></a>2. skapa det virtuella nätverket

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

Gateway-undernätet innehåller de reserverade IP-adresser som används av tjänsten för virtuell nätverksgateway. Använd följande exempel för att lägga till och ange ett Gateway-undernät:

Lägg till gateway-undernätet.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ange Gateway-undernätets konfiguration för det virtuella nätverket.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. begär en offentlig IP-adress
 
I det här steget väljer du de instruktioner som gäller för den gateway som du vill skapa. Valet av zoner för att distribuera gatewayen beror på de zoner som anges för den offentliga IP-adressen.

### <a name="ipzoneredundant"></a>För zoner-redundanta gateways

Begär en offentlig IP-adress med en **standard** -PUBLICIPADDRESS-SKU och ange ingen zon. I det här fallet är den standardiserade offentliga IP-adress som skapas en zon-redundant offentlig IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>För zonindelade-gatewayer

Begär en offentlig IP-adress med en **standard** -PUBLICIPADDRESS-SKU. Ange zonen (1, 2 eller 3). Alla gateway-instanser kommer att distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>För regionala gatewayer

Begär en offentlig IP-adress med en **grundläggande** PUBLICIPADDRESS-SKU. I det här fallet distribueras gatewayen som en regional gateway och har ingen inbyggd zon redundans i gatewayen. Gateway-instanserna skapas i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. skapa IP-konfigurationen

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. skapa gatewayen

Skapa den virtuella Nätverksgatewayen.

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>För VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>Vanliga frågor och svar

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad kommer att ändras när jag distribuerar dessa nya SKU: er?

I ditt perspektiv kan du distribuera dina gateways med zon-redundans. Det innebär att alla instanser av gatewayerna distribueras över Azure-tillgänglighetszoner och att varje tillgänglighets zon är ett annat fel och en annan uppdaterings domän. Detta gör dina gatewayer mer pålitliga, tillgängliga och elastiska för zon haverier.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure Portal?

Ja, du kan använda Azure Portal för att distribuera de nya SKU: erna. Du kommer dock bara att se dessa nya SKU: er i de Azure-regioner som har Azure-tillgänglighetszoner.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU: erna?

Se [Tillgänglighetszoner](../availability-zones/az-overview.md#services-support-by-region) för den senaste listan över tillgängliga regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera mina befintliga virtuella nätverks-gatewayer till zoner-redundanta eller zonindelade-gatewayer?

Det finns för närvarande inte stöd för migrering av befintliga virtuella nätverksgateway till zoner-redundanta eller zonindelade-gatewayer. Du kan dock ta bort din befintliga gateway och återskapa en zon-redundant eller zonindelade Gateway.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera både VPN-och Express-flödesnoder i samma virtuella nätverk?

Samtidig användning av både VPN-och Express-flödesnoder i samma virtuella nätverk stöds. Du bör dock reservera ett/27 IP-adressintervall för gateway-undernätet.
