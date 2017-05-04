---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats: PowerShell | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 4ec11ffeae94b4a8e5a65566f0f0c067f45a0134
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Skapa ett VNet med en VPN-anslutning för plats-till-plats med hjälp av PowerShell

Den här artikeln visar hur du kan använda PowerShell för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
>


![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Verifiera att du vill arbeta med distributionsmodellen i Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* En extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* Den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="example-values"></a>Exempelvärden

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

```
#Example values

VnetName                = testvnet 
ResourceGroup           = testrg 
Location                = West US 
AddressSpace            = 10.0.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.0.1.0/28 
GatewaySubnet           = 10.0.0.0/27
LocalNetworkGatewayName = LocalSite
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24','20.0.0.0/24
Gateway Name            = vnetgw1
PublicIP                = gwpip
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = myGWConnection
```

## <a name="Login"></a>1. Ansluta till din prenumeration
Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

1. Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Login-AzureRmAccount
  ```
2. Kontrollera prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Ange den prenumeration som du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

## <a name="VNet"></a>2. Skapa ett virtuellt nätverk och ett gateway-undernät.

Om du inte redan har ett virtuellt nätverk, skapa ett. När du skapar ett virtuellt nätverk ska du kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket. För den här konfigurationen behöver du också ett gateway-undernät. Den virtuella nätverksgatewayen använder ett gatewayundernät som innehåller de IP-adresser som VPN-gatewaytjänsterna använder. När du skapar ett gateway-undernät måste det få namnet ”GatewaySubnet”. Om du ger det något annat namn kan du skapa undernätet, men Azure behandlar det inte som ett gateway-undernät.

Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja /27 eller /28. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Så här skapar du ett virtuellt nätverk och ett gateway-undernät

I det här exemplet skapas ett virtuellt nätverk och ett gateway-undernät. Om du redan har ett virtuellt nätverk som du behöver lägga till ett gateway-undernät för kan du läsa [Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat](#gatewaysubnet).

Skapa en resursgrupp:

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Skapa ditt virtuella nätverk. 

1. Ange variablerna.

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
  ```
2. Skapa VNet.

  ```powershell
  New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
  -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat

1. Ange variablerna.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
  ```
2. Skapa gateway-undernätet.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/27 -VirtualNetwork $vnet
  ```
3. Ange konfigurationen.

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3. <a name="localnet"></a>Skapa den lokala nätverksgatewayen

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

Ange följande värden:

* *GatewayIPAddress* är IP-adressen till den lokala VPN-enheten. VPN-enheten får inte finnas bakom en NAT.
* *AddressPrefix* är ditt lokala adressutrymme.

- Så här lägger du till en lokal nätverksgateway med ett enda adressprefix:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix   '10.0.0.0/24'
  ```

- Så här lägger du till en lokal nätverksgateway med flera adressprefix:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

- Så här ändrar du IP-adressprefixen för en lokal nätverksgateway:<br>
Ibland ändras prefixen för din lokala nätverksgateway. Vilka steg du utför för att ändra IP-adressprefixen beror på om du har skapat en VPN Gateway-anslutning. Se artikelavsnittet [Ändra IP-adressprefix för en lokal nätverksgateway](#modify).

## <a name="PublicIP"></a>4. Begär en offentlig IP-adress

Begär en offentlig IP-adress som ska allokeras till din virtuella nätverks-VPN-gateway. Det här är IP-adressen du konfigurerar att VPN-enheten ska anslutas till.

Den virtuella nätverksgatewayen för Resource Manager-distributionsmodellen stöder för närvarande endast offentliga IP-adresser med hjälp av dynamisk fördelning. Detta betyder dock inte IP-adressen kan ändras. Den enda gången VPN-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Gatewayens offentliga IP-adress ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din Azure VPN-gateway.

Använd följande PowerShell-exempel:

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Skapa gatewayens konfiguration av IP-adressering
Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Skapa VPN-gatewayen

Skapa den virtuella VPN-nätverksgatewayen. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

Ange följande värden:

* *-GatewayType* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Andra gateway-konfigurationer kan kräva -GatewayType ExpressRoute.
* *-VpnType* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Mer information om VPN-gatewaytyper finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).
* *-GatewaySku* kan vara be Basic, Standard eller HighPerformance. Det finns konfigurationsbegränsningar för vissa SKU:er. Se [Gateway SKU:er](vpn-gateway-about-vpngateways.md#gateway-skus) för mer information.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. Konfigurera din VPN-enhet

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

Använd följande exempel för att hitta den offentliga IP-adressen för din virtuella nätverksgateway med hjälp av PowerShell:

```powershell
Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
```

## <a name="CreateConnection"></a>8. Skapa VPN-anslutningen
Därefter skapar du VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Glöm inte att byta ut värdena mot dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. Observera att '-ConnectionType' för plats-till-plats är *IPsec*.

1. Ange variablerna.
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
  $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
  ```

2. Skapa anslutningen.
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName testrg `
  -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

Efter en kort stund har anslutningen upprättats.

## <a name="toverify"></a>9. Verifiera VPN-anslutningen
Det finns några olika metoder för att verifiera VPN-anslutningen.

[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="modify"></a>Så här ändrar du IP-adressprefixen för en lokal nätverksgateway
Använd följande anvisningar om du behöver ändra prefixen för din lokala nätverksgateway. Det finns två uppsättningar med instruktioner. Vilken du väljer beror på om du redan har skapat din gateway-anslutning.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Så här ändrar du gateway-IP-adressen för en lokal nätverksgateway
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg
*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).

