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
ms.date: 05/31/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 9edaa81111d9439bfbad4775e49c3e29454ad31f
ms.contentlocale: sv-se
ms.lasthandoff: 07/15/2017

---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Skapa ett VNet med en VPN-anslutning för plats-till-plats med hjälp av PowerShell

Den här artikeln visar hur du kan använda PowerShell för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk portal (klassisk)](vpn-gateway-site-to-site-create.md)
> 
>


En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. PowerShell-cmdlets uppdateras ofta och vanligen måste du uppdatera dina PowerShell-cmdlets för att få den senaste funktionen. Om du inte uppdaterar din PowerShell-cmdlet misslyckas värdena som har angetts. Mer information om hur man hämtar oc installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

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

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2. Skapa ett virtuellt nätverk och ett gateway-undernät.

Om du inte redan har ett virtuellt nätverk, skapa ett. När du skapar ett virtuellt nätverk ska du kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

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

Så här lägger du till en lokal nätverksgateway med ett enda adressprefix:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

Så här lägger du till en lokal nätverksgateway med flera adressprefix:

  ```powershell
  New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
  -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

Så här ändrar du IP-adressprefixen för en lokal nätverksgateway:<br>
Ibland ändras prefixen för din lokala nätverksgateway. Vilka steg du utför för att ändra IP-adressprefixen beror på om du har skapat en VPN Gateway-anslutning. Se artikelavsnittet [Ändra IP-adressprefix för en lokal nätverksgateway](#modify).

## <a name="PublicIP"></a>4. Begär en offentlig IP-adress

En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN-gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

Begär en offentlig IP-adress som ska tilldelas till din virtuella nätverks-VPN-gateway.

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
* Markera den gateway SKU som du vill använda. Det finns konfigurationsbegränsningar för vissa SKU:er. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information. Om du får ett felmeddelande när du skapar VPN-gatewayen för GatewaySku, kontrollerar du att du har installerat den senaste versionen av PowerShell-cmdlets.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7. Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Använd följande exempel för att hitta den offentliga IP-adressen för din virtuella nätverksgateway med hjälp av PowerShell:

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


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

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Ändra IP-adressprefixen för en lokal nätverksgateway

Om IP-adressprefixen du vill ska dirigeras till din lokala plats ändras kan du ändra din lokala nätverksgateway. Det finns två uppsättningar med instruktioner. Vilken du väljer beror på om du redan har skapat din gateway-anslutning.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Ändra gateway-IP-adressen för en lokal nätverksgateway

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).

