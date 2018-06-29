---
title: Skapa en zonredundant virtuell nätverksgateway i Azure tillgänglighet zoner - Preview | Microsoft Docs
description: Distribuera VPN-Gateway och ExpressRoute-gatewayer i tillgänglighet zoner - förhandsgranskning.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: c484358bf98f0121cfc3ce270b162b01c75b5b09
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096241"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Skapa en zonredundant virtuell nätverksgateway i Azure tillgänglighet zoner - förhandsgranskning

Du kan distribuera VPN- och ExpressRoute-gateway i [Azure tillgänglighet zoner](../availability-zones/az-overview.md). Detta ger högre tillgänglighet, skalbarhet och återhämtning för virtuella nätverksgatewayer. Distribuera gateways i Azure tillgänglighet zoner fysiskt och logiskt separerar gateways inom en region samtidigt skydda din lokala nätverksanslutning till Azure från fel på zonnivå.

Zonal och zonredundant gatewayer har grundläggande prestandaförbättringar över vanliga virtuella nätverksgatewayerna. Dessutom är skapar en zonredundant eller zonal virtuell nätverksgateway snabbare än att skapa andra gateways. Skapa tider tar ungefär 15 minuter för en ExpressRoute-gateway och 19 minuter för en VPN-gateway i stället för med 45 minuter.

### <a name="zrgw"></a>Zonredundant gateways

För att automatiskt distribuera din virtuella nätverksgatewayerna över tillgänglighet zoner, kan du använda zonredundant virtuella nätverksgatewayerna. Med zonredundant gateways, kan du utnyttja 99,99% drifttid SLA på GA åtkomst till dina kritiska, skalbara tjänster på Azure.

<br>
<br>

![zonen redunant gateways bild](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonal gateways

Om du vill distribuera gateways i en viss zon kan du använda zonal gateways. När du distribuerar en zonal gateway distribueras båda instanser av gatewayen i samma zon tillgänglighet.

<br>
<br>

![Bild av zonal gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Gateway-SKU:er

Zonredundant och zonal gateway måste använda den nya gatewayen SKU: er. När du [självregistrerar i förhandsgranskningen](#enroll), visas den nya virtuella nätverksgatewayen SKU: er i alla Azure AZ regioner. Dessa SKU: er liknar motsvarande SKU: er för ExpressRoute- och VPN-Gateway, förutom att de är specifika för zonredundant och zonal gateways.

Ny gateway-SKU: er är:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Offentliga IP-SKU: er

Zonredundant gatewayer och zonal gatewayer förlitar sig på Azure offentliga IP-resurs *Standard* SKU. Konfigurationen av Azure offentliga IP-resurs avgör om den gateway som du distribuerar är zonredundant, eller zonal. Om du skapar en offentlig IP-resurs med en *grundläggande* SKU, gatewayen kommer inte att ha några redundans och gateway-resurser är regionala.

### <a name="pipzrg"></a>Zonredundant gateways

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU utan att ange en zon, beteendet skiljer sig åt beroende på om gatewayen är en VPN-gateway eller en ExpressRoute-gateway. 

* Två gateway-instanser ska distribueras i alla 2 utanför dessa tre zoner att tillhandahålla redundans för en VPN-gateway. 
* För en ExpressRoute-gateway, eftersom det kan finnas fler än två instanser gatewayen kan sträcka sig över alla tre zoner.

### <a name="pipzg"></a>Zonal gateways

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU och ange zonen (1, 2 eller 3), gateway-instanser som ska distribueras i samma zon.

### <a name="piprg"></a>Regional gateways

När du skapar en offentlig IP-adress med hjälp av den **grundläggande** offentliga IP-SKU gateway distribueras som en gateway för regional och inte har några-redundans är inbyggda i gatewayen.

## <a name="before-you-begin"></a>Innan du börjar

Du kan använda antingen PowerShell lokalt installerade på datorn eller gränssnitt för Azure-molnet. Om du väljer att installera och använda PowerShell lokalt i den här funktionen kräver den senaste versionen av PowerShell-modulen.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Du använder PowerShell lokalt

Om du använder PowerShell lokalt på datorn i stället för med molnet Shell, måste du installera PowerShell-modulen 6.1.1 eller högre. Kontrollera vilken version av PowerShell som du har installerat, använder du följande kommando:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Registrera dig i förhandsgranskningen

Innan du kan konfigurera en gateway zonredundant eller zonal, måste du först registrera själva prenumerationen i förhandsgranskningen. När din prenumeration har etablerats startar att se den nya gatewayen SKU: er i alla Azure AZ regioner. 

Kontrollera att du är inloggad på ditt Azure-konto och använder den prenumeration som du vill godkända för den här förhandsgranskningen. Använd följande exempel för att registrera:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Använd följande kommando för att kontrollera att funktionen 'AllowVMSSVirtualNetworkGateway' har registrerats med din prenumeration:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Resultatet ser ut ungefär så här:

![etablerats](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Deklarera dina variabler

De värden som används i exempel steg visas nedan. Dessutom använder vissa av exemplen deklarerade variabler i stegen. Om du använder de här stegen i din egen miljö måste du ersätta värdena med dina egna. När du anger plats, måste du kontrollera att den region som du anger stöds. Mer information finns i [vanliga frågor och svar](#faq).

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

## <a name="configure"></a>3. Skapa virtuella nätverk

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Skapa ett virtuellt nätverk.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. Lägg till gateway-undernätet

Gateway-undernätet innehåller reserverade IP-adresser med gateway-tjänster för virtuella nätverk. Använd följande exempel för att lägga till och ange ett gateway-undernät:

Lägg till gateway-undernätet.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ange konfigurationen för gateway-undernät för det virtuella nätverket.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Begär en offentlig IP-adress
 
I det här steget väljer du de anvisningar som gäller för den gateway som du vill skapa. Valet av zoner för att distribuera gateways beror på de zoner som angetts för den offentliga IP-adressen.

### <a name="ipzoneredundant"></a>För zonredundant gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU och inte anger någon zon. I det här fallet blir Standard offentliga IP-adressen skapas en zonredundant offentliga IP-adress.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>För zonal gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU. Ange zonen (1, 2 eller 3). Alla gateway-instanser ska distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>För regional gateways

Begär en offentlig IP-adress med en **grundläggande** PublicIpaddress SKU. I det här fallet gatewayen distribueras som en gateway för regional och har inte någon-redundans är inbyggda i gatewayen. Gateway-instanser skapas respektive i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Skapa IP-konfiguration

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Skapa gatewayen

Skapa den virtuella nätverksgatewayen.

>[!NOTE]
>Du kan inte ange gateway SKU för tillfället. SKU: N blir automatiskt som standard ErGw1AZ för ExpressRoute- och VpnGw1AZ för VPN-Gateway.
>

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>För VPN-Gateway

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Ge feedback

Vi gärna dina synpunkter. Skicka ett e-postmeddelande till aznetworkgateways@microsoft.com att rapportera eventuella problem eller ger feedback (positivt eller negativt) för zonredundant och zonal VPN och Express Route gateway. Företagsnamnet i ”[]” i ämnesraden. Även innehålla ditt prenumerations-ID om du rapporterar ett problem.

## <a name="faq"></a>Vanliga frågor och svar

### <a name="how-do-i-sign-up-for-the-preview"></a>Hur loggar jag för förhandsgranskningen?

Du kan [självregistrerar](#enroll) med PowerShell-kommandon i den här artikeln.

### <a name="what-will-change-when-i-enroll"></a>Vad ändras när jag registrerar?

Under förhandsgranskningen gör din ur kan du distribuera din gateway med redundans. Detta innebär att alla instanser av gateway ska distribueras till Azure tillgänglighet zoner och varje zon tillgänglighet är en annan fel- och update-domän. Detta gör dina gatewayer tillförlitliga, tillgänglighet och flexibel för zonen fel.

### <a name="what-regions-are-available-for-the-preview"></a>Vilka regioner är tillgängliga för förhandsgranskningen?

Zonredundant och zonal gateway finns i produktion/Azure offentliga områden.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Jag debiteras för deltagande i den här förhandsgranskningen?

Du kommer inte att debiteras för din gateways under förhandsgranskningen. Det finns dock ingen SLA som är kopplade till din distribution. Vi är mycket intresserade höra dina synpunkter.

> [!NOTE]
> ExpressRoute-gatewayer är gatewayen inte debiteras/debiteras. Kretsen sig själv (inte gateway) kommer att debiteras.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Vilka regioner är tillgängliga för mig på detta i?

Förhandsversion är tillgängligt i centrala USA och Frankrike centrala regioner (Azure-regioner som har tillgänglighet zoner allmänt tillgänglig). Framöver, kommer att Zonredundant gateway tillgängliga för dig i andra offentliga Azure-regioner.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra Mina befintliga virtuella nätverksgatewayerna till zonredundant eller zonal gateways?

Migrera dina befintliga virtuella nätverksgatewayerna till zonredundant eller zonal gateways stöds inte för närvarande. Du kan dock ta bort den befintliga gatewayen och skapa en gateway zonredundant eller zonal igen.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera VPN- och Expressroute-gateway i samma virtuella nätverk?

VPN- och Expressroute-gateway i samma virtuella nätverk existerar stöds under förhandsversion. Dock vara medveten om följande krav och begränsningar:

* Reservera en minst/27 IP-adressintervall för gateway-undernätet.
* Zonen-redundanta/zonal Express Route-gatewayer kan bara finnas tillsammans med zonen-redundanta/zonal VPN-gatewayer.
* Distribuera en zon-redundanta/zonal Express Route-gateway innan du distribuerar zonen-redundanta/zonal VPN-gatewayen.
* En zon-redundanta/zonal Express Route-gateway kan anslutas till högst 4 kretsar.

## <a name="next-steps"></a>Nästa steg

Vi gärna dina synpunkter. Skicka ett e-postmeddelande till aznetworkgateways@microsoft.com att rapportera eventuella problem eller ger feedback (positivt eller negativt) för zonredundant och zonal VPN och Express Route gateway. Företagsnamnet i ”[]” i ämnesraden. Även innehålla ditt prenumerations-ID om du rapporterar ett problem.
