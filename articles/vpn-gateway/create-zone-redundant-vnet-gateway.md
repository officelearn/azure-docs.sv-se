---
title: Skapa en zonredundant virtuell nätverksgateway i Tillgänglighetszoner i Azure - förhandsversionen | Microsoft Docs
description: Distribuera VPN-Gateway och ExpressRoute-gatewayer i Tillgänglighetszoner - förhandsversion.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952563"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Skapa en zonredundant virtuell nätverksgateway i Azure Availability Zones – förhandsversion

Du kan distribuera VPN och ExpressRoute-gatewayer i [Azure Availability Zones](../availability-zones/az-overview.md). Detta ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribuera gateways i Azure Availability Zones fysisk och logiskt separerar gatewayer inom en region, samtidigt som du skyddar din lokal nätverksanslutning till Azure från zonen på servernivå fel.

Zonindelade och zonredundanta gateway är tilldelad grundläggande prestandaförbättringar över vanliga virtuella nätverksgatewayer. Skapa en zonredundant eller zonindelad virtuell nätverksgateway är dessutom snabbare än att skapa andra gatewayer. I stället för att ta 45 minuter att skapa gånger tar ungefär 15 minuter för en ExpressRoute-gateway och 19 minuter för en VPN-gateway.

### <a name="zrgw"></a>Zonredundant-gatewayer

För att automatiskt distribuera din virtuella nätverksgatewayer mellan tillgänglighetszoner, kan du använda zonredundant virtuella nätverksgatewayer. Med zonredundant gatewayer, kan du dra nytta 99,99% DRIFTTID vid GA åtkomst till dina verksamhetskritiska, skalbara tjänster på Azure.

<br>
<br>

![zon redunant gatewayer bild](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Zonindelade gateways

För att distribuera gateways i en viss zon kan använda du zonindelad gatewayer. När du distribuerar en zonindelad gateway distribueras båda instanserna av gatewayen i samma Tillgänglighetszon.

<br>
<br>

![zonindelade gatewayer bild](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>Gateway-SKU:er

Zonredundant och zonindelade gatewayer måste använda den nya gatewayen SKU: er. När du [själv i förhandsversionen av](#enroll), visas den nya virtuella nätverksgatewayen SKU: er i alla Azure-AZ-regioner. Dessa SKU: er liknar de motsvarande SKU: er för ExpressRoute och VPN-Gateway, förutom att de är specifika för zonredundant och zonindelade gatewayer.

Den nya gatewayen SKU: er är:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Offentliga IP-SKU: er

Zonredundant gateway och zonindelade gateway förlitar sig på Azure offentlig IP-adressresurs *Standard* SKU. Konfigurationen av Azure offentlig IP-adressresurs avgör om den gateway som du distribuerar är zonredundant, eller zonindelade. Om du skapar en offentlig IP-resurs med en *grundläggande* SKU, gatewayen har inte någon redundans och gateway-resurserna blir regionala.

### <a name="pipzrg"></a>Zonredundant-gatewayer

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU utan att ange en zon, beteendet skiljer sig beroende på om gatewayen är en VPN-gateway eller en ExpressRoute-gateway. 

* Två gateway-instanserna ska distribueras i 2 utanför dessa tre zoner att tillhandahålla redundans för en VPN-gateway. 
* För en ExpressRoute-gateway, eftersom det kan finnas fler än två instanser kan gatewayen omfatta flera alla tre zoner.

### <a name="pipzg"></a>Zonindelade gateways

När du skapar en offentlig IP-adress med hjälp av den **Standard** offentliga IP-SKU och ange zonen (1, 2 eller 3), alla gatewayinstanser ska distribueras i samma zon.

### <a name="piprg"></a>Regionala gateways

När du skapar en offentlig IP-adress med hjälp av den **grundläggande** offentliga IP-SKU gatewayen distribueras som en regional gateway och inte har någon zonredundans som är inbyggda i gatewayen.

## <a name="before-you-begin"></a>Innan du börjar

Du kan använda antingen PowerShell installerat lokalt på datorn eller Azure Cloud Shell. Om du väljer att installera och använda PowerShell lokalt kräver den senaste versionen av PowerShell-modulen i den här funktionen.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Att använda PowerShell lokalt

Om du använder PowerShell lokalt på datorn i stället för att använda Cloud Shell måste du installera PowerShell-modulen 6.1.1 eller högre. Om du vill kontrollera vilken version av PowerShell som du har installerat, använder du följande kommando:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Registrera dig i förhandsversionen

Innan du kan konfigurera en gateway för zonredundant eller zonindelad, måste du först registrera själva prenumerationen i förhandsversionen. När din prenumeration har etablerats, börjar du se den nya gatewayen SKU: er i alla regioner för Azure-AZ. 

Se till att du är inloggad på ditt Azure-konto och använder den prenumeration som du vill godkänna för den här förhandsversionen. Använd följande exempel för att registrera:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Använd följande kommando för att verifiera att funktionen 'AllowVMSSVirtualNetworkGateway' är registrerat med din prenumeration:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Resultatet ser ut ungefär som det här exemplet:

![etablerad](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Deklarera dina variabler

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

## <a name="configure"></a>3. Skapa det virtuella nätverket

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

Gateway-undernätet innehåller reserverade IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Använd följande exempel för att lägga till och ange ett gateway-undernät:

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
 
I det här steget, väljer du de anvisningar som gäller för gateway som du vill skapa. Valet av zoner för att distribuera gateway beror på de zoner som angetts för den offentliga IP-adressen.

### <a name="ipzoneredundant"></a>För zonredundant gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU och inte anger någon zon. I det här fallet blir Standard offentliga IP-adressen skapas en zonredundant offentlig IP-adress.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>För zonindelade gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU. Ange zonen (1, 2 eller 3). Alla gatewayinstanser ska distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>För regionala gateways

Begär en offentlig IP-adress med en **grundläggande** PublicIpaddress SKU. I det här fallet gatewayen distribueras som en regional gateway och har inte någon zonredundans som är inbyggda i gatewayen. Gateway-instanser skapas respektive i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Skapa IP-konfigurationen

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Skapa gatewayen

Skapa den virtuella nätverksgatewayen.

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>För VPN-Gateway

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Ge feedback

Vi gärna dina synpunkter. Skicka ett e- aznetworkgateways@microsoft.com att rapportera problem eller ge feedback (positivt eller negativt) för zonredundant och zonindelade VPN och Expressroute-gatewayer. Inkludera företagets namn i ”[]” i ämnesraden. Även innehålla ditt prenumerations-ID om du rapporterar ett problem.

## <a name="faq"></a>Vanliga frågor och svar

### <a name="how-do-i-sign-up-for-the-preview"></a>Hur registrerar jag mig för förhandsversionen?

Du kan [själv](#enroll) med hjälp av PowerShell-kommandon i den här artikeln.

### <a name="what-will-change-when-i-enroll"></a>Vad ändras när jag registrerar?

I förhandsversionen från ditt perspektiv, kan du distribuera dina gatewayer med redundans. Det innebär att alla instanser av gateway som ska distribueras till Azure Availability Zones och varje Tillgänglighetszon är en annan fel- och domän. Detta gör dina gatewayer mer tillförlitlig, tillgängliga och elastiska zon fel.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure-portalen?

Ja, du kan använda Azure-portalen för förhandsversionen. Men du fortfarande behöver registreras med hjälp av PowerShell eller du längre inte använda portalen för förhandsversionen.

### <a name="what-regions-are-available-for-the-preview"></a>Vilka regioner är tillgängliga för förhandsgranskning?

Zonredundant och zonindelade gatewayer finns i offentliga produktions-/ Azure-regioner.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Faktureras jag för att delta i den här förhandsversionen?

Du kommer inte att debiteras för dina gatewayer under förhandsversionen. Det finns dock inget serviceavtal som är kopplat till din distribution. Vi är väldigt intresserade höra dina synpunkter.

> [!NOTE]
> För ExpressRoute-gatewayer är gatewayen inte faktureras/debiteras. Kretsen själva (inte gatewayen) kommer att debiteras.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Vilka regioner är tillgängliga för mig att prova i?

Den offentliga förhandsversionen är tillgänglig i regionerna USA, centrala och Frankrike, centrala (Azure-regioner som har Tillgänglighetszoner som är allmänt tillgänglig). Framöver kommer tillgängliggör vi Zonredundant gateway till dig i andra offentliga Azure-regioner.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra Mina befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad-gatewayer?

Migrera dina befintliga virtuella nätverksgatewayer för zonredundant eller zonindelad gatewayer stöds för närvarande inte. Du kan dock ta bort den befintliga gatewayen och skapa en zonredundant eller zonindelad gateway igen.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera VPN- och Expressroute-gatewayer i samma virtuella nätverk?

Samtidig både VPN och Expressroute-gatewayer i samma virtuella nätverk stöds för den offentliga förhandsversionen. Men tänk på följande krav och begränsningar:

* Reservera en /27 IP-adressintervall för gateway-undernätet.
* Zon-redundant/zonindelad Express Route-gatewayer kan endast finnas tillsammans med zon-redundant/zonindelad VPN-gatewayer.
* Distribuera en zon-redundant/zonindelad Express Route-gateway innan du distribuerar en zon-redundant/zonindelad VPN-gateway.
* En zon-redundant/zonindelad Express Route-gateway kan anslutas till högst 4 kretsar.

## <a name="next-steps"></a>Nästa steg

Vi gärna dina synpunkter. Skicka ett e- aznetworkgateways@microsoft.com att rapportera problem eller ge feedback (positivt eller negativt) för zonredundant och zonindelade VPN och Expressroute-gatewayer. Inkludera företagets namn i ”[]” i ämnesraden. Även innehålla ditt prenumerations-ID om du rapporterar ett problem.
