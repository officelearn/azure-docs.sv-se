---
title: 'Azure VPN Gateway: Anslut gateways till flera lokala principbaserade VPN-enheter'
description: Konfigurera en Azure-flödesbaserad VPN-gateway till flera principbaserade VPN-enheter med Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123326"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Ansluta Azure VPN-gateways till flera lokala principbaserade VPN-enheter med PowerShell

Den här artikeln hjälper dig att konfigurera en Azure-dirigerarbaserad VPN-gateway för att ansluta till flera lokala principbaserade VPN-enheter som utnyttjar anpassade IPsec/IKE-principer för S2S VPN-anslutningar.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Om principbaserade och ruttbaserade VPN-gateways

Principbaserade *vpn-enheter jämfört med* ruttbaserade skiljer sig åt i hur IPsec-trafikväljare ställs in på en anslutning:

* **Policybaserad** VPN-enheter använder kombinationer av prefix från båda nätverken för att definiera hur trafiken krypteras/dekrypteras via IPsec-tunnlar. Det är vanligtvis byggt på brandväggsenheter som utför paketfiltrering. Kryptering och dekryptering av IPsec-tunnlar läggs till i motorn för paketfiltrering och bearbetning.
* **Ruttbaserad** VPN-enheter använder valfria (jokertecken) trafikväljare och låter routnings-/vidarebefordran av tabeller dirigera trafik till olika IPsec-tunnlar. Den är vanligtvis byggd på routerplattformar där varje IPsec-tunnel modelleras som ett nätverksgränssnitt eller VTI (virtuellt tunnelgränssnitt).

Följande diagram markerar de två modellerna:

### <a name="policy-based-vpn-example"></a>Principbaserat VPN-exempel
![policybaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exempel på ruttbaserad VPN
![ruttbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-stöd för principbaserad VPN
För närvarande stöder Azure båda lägena för VPN-gateways: ruttbaserade VPN-gateways och principbaserade VPN-gateways. De är byggda på olika interna plattformar, vilket resulterar i olika specifikationer:

|                          | **Principbaserad VPN-gateway** | **Ruttbaserad VPN-gateway**       |**Ruttbaserad VPN-gateway**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure Gateway SKU**    | Basic                       | Basic                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **IKE-version**          | IKEv1                       | IKEv2                            | IKEv1 och IKEv2                         |
| **Max. S2S-anslutningar** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Med den anpassade IPsec/IKE-principen kan du nu konfigurera Azure-ruttbaserade VPN-gateways för att använda prefixbaserade trafikväljare med alternativet "**PolicyBasedTrafficSelectors**", för att ansluta till lokala principbaserade VPN-enheter. Med den här funktionen kan du ansluta från ett virtuellt Azure-nätverk och VPN-gateway till flera lokala principbaserade VPN/brandväggsenheter, vilket tar bort den enda anslutningsgränsen från de aktuella Azure-principbaserade VPN-gatewayerna.

> [!IMPORTANT]
> 1. För att aktivera den här anslutningen måste dina lokala principbaserade VPN-enheter ha stöd för **IKEv2** för att ansluta till Azure-dirigerarbaserade VPN-gateways. Kontrollera specifikationerna för VPN-enheten.
> 2. De lokala nätverk som ansluter via principbaserade VPN-enheter med den här mekanismen kan bara ansluta till det virtuella Azure-nätverket. **De kan inte transitera till andra lokala nätverk eller virtuella nätverk via samma Azure VPN-gateway**.
> 3. Konfigurationsalternativet är en del av den anpassade IPsec/IKE-anslutningsprincipen. Om du aktiverar alternativet principbaserad trafikväljare måste du ange den fullständiga principen (IPsec/IKE-kryptering och integritetsalgoritmer, nyckelstyrkor och SA-livstider).

Följande diagram visar varför transitroutning via Azure VPN-gateway inte fungerar med det principbaserade alternativet:

![policybaserad transitering](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Som visas i diagrammet har Azure VPN-gatewayen trafikväljare från det virtuella nätverket till var och en av de lokala nätverksprefixen, men inte prefixen för korsanslutning. Till exempel kan lokal plats 2, plats 3 och plats 4 kommunicera med VNet1 respektive, men kan inte ansluta via Azure VPN-gatewayen till varandra. Diagrammet visar de trafikväljare för korsanslutning som inte är tillgängliga i Azure VPN-gatewayen under den här konfigurationen.

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Instruktionerna i den här artikeln följer samma exempel som beskrivs i [Konfigurera IPsec/IKE-principen för S2S- eller VNet-till-VNet-anslutningar för](vpn-gateway-ipsecikepolicy-rm-powershell.md) att upprätta en S2S VPN-anslutning. Detta visas i följande diagram:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Arbetsflödet för att aktivera den här anslutningen:
1. Skapa det virtuella nätverket, VPN-gatewayen och den lokala nätverksgatewayen för din anslutning över gränserna.
2. Skapa en IPsec/IKE-princip.
3. Använd principen när du skapar en S2S- eller VNet-till-VNet-anslutning och **aktiverar de principbaserade trafikväljare på** anslutningen.
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen till en befintlig anslutning.

## <a name="before-you-begin"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Aktivera principbaserade trafikväljare

I det här avsnittet visas hur du aktiverar principbaserade trafikväljare på en anslutning. Kontrollera att du har slutfört [del 3 i principartikeln Konfigurera IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). I stegen i den här artikeln används samma parametrar.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Steg 1 – Skapa det virtuella nätverket, VPN-gatewayen och den lokala nätverksgatewayen

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Anslut till din prenumeration och deklarera dina variabler

1. Om du kör PowerShell lokalt på datorn loggar du in med cmdleten *Connect-AzAccount.* Eller använd i stället Azure Cloud Shell i webbläsaren.

2. Deklarera dina variabler. För den här övningen använder vi följande variabler:

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Skapa det virtuella nätverket, VPN-gatewayen och den lokala nätverksgatewayen

1. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Använd följande exempel för att skapa det virtuella nätverket TestVNet1 med tre undernät och VPN-gatewayen. Om du vill ersätta värden är det viktigt att du alltid namnger gateway-undernätet specifikt "GatewaySubnet". Om du ger det något annat namn går det inte att skapa gatewayen.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Steg 2 - Skapa en S2S VPN-anslutning med en IPsec/IKE-princip

1. Skapa en IPsec/IKE-princip.

   > [!IMPORTANT]
   > Du måste skapa en IPsec/IKE-princip för att aktivera alternativet "UsePolicyBasedTrafficSelectors" på anslutningen.

   I följande exempel skapas en IPsec/IKE-princip med dessa algoritmer och parametrar:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, SA Lifetime 14400 sekunder & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Skapa S2S VPN-anslutningen med principbaserade trafikväljare och IPsec/IKE-principen och tillämpa IPsec/IKE-principen som skapades i föregående steg. Var medveten om den ytterligare parametern "-UsePolicyBasedTrafficSelectors $True", som möjliggör principbaserade trafikväljare på anslutningen.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. När du har slutfört stegen använder S2S VPN-anslutningen den definierade IPsec/IKE-principen och aktiverar principbaserade trafikväljare på anslutningen. Du kan upprepa samma steg för att lägga till fler anslutningar till ytterligare lokala principbaserade VPN-enheter från samma Azure VPN-gateway.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Så här uppdaterar du principbaserade trafikväljare
I det här avsnittet visas hur du uppdaterar alternativet principbaserade trafikväljare för en befintlig S2S VPN-anslutning.

1. Hämta anslutningsresursen.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Visa alternativet principbaserade trafikväljare.
Följande rad visar om de principbaserade trafikväljare används för anslutningen:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Om raden returnerar "**True**" konfigureras principbaserade trafikväljare på anslutningen. annars returnerar den "**Falskt**.".
1. När du har hämtat anslutningsresursen kan du aktivera eller inaktivera de principbaserade trafikväljare på en anslutning.

   - Så här aktiverar du

      I följande exempel kan alternativet principbaserade trafikväljare, men IPsec/IKE-principen lämnas oförändrad:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Så här inaktiverar du

      I följande exempel inaktiveras alternativet principbaserade trafikväljare, men IPsec/IKE-principen lämnas oförändrad:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.

Läs även [Konfigurera IPsec/IKE-principen för S2S VPN- eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) för mer information om anpassade IPsec/IKE-principer.
