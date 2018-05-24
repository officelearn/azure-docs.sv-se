---
title: Skapa och hantera VPN-anslutningar i Azure mellan servrar med hjälp av PowerShell | Microsoft Docs
description: Självstudie – Skapa och hantera VPN-anslutningar mellan servrar med Azure PowerShell-modulen
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Skapa och hantera VPN-anslutningar mellan servrar med Azure PowerShell-modulen

VPN-anslutningar mellan servrar i Azure ger säker anslutning mellan olika platser, t.ex. mellan kundens lokaler och Azure. Den här självstudien visar VPN-anslutningens livscykel via IPsec mellan servrar, till exempel att skapa och hantera en VPN-anslutning mellan servrar. Lär dig att:

> [!div class="checklist"]
> * Skapa en VPN-anslutning mellan servrar
> * Uppdatera anslutningsegenskapen: i förväg delad nyckel, BGP, IPsec/IKE-princip
> * Lägga till flera VPN-anslutningar
> * Ta bort en VPN-anslutning

Följande diagram visar topologin för den här självstudien:

![Diagram över VPN-anslutning för plats till plats](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien version 5.3 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="requirements"></a>Krav

Slutför den första självstudien ”:[Skapa VPN-gateway med Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md)” för att skapa följande resurser:

1. Resursgrupp (TestRG1), virtuellt nätverk (VNet1) och GatewaySubnet
2. VPN-gateway (VNet1GW)

Nedan visas parametervärdena för det virtuella nätverket. Lägg märke till ytterligare värden för den lokala nätverksgatewayen som motsvarar ditt lokala nätverk. Ändra värdena baserat på din miljö och nätverkskonfiguration.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Arbetsflödet för att skapa en VPN-anslutning mellan servrar är enkelt:

1. Skapa en lokal nätverksgateway som representerar det lokala nätverket
2. Skapa en anslutning mellan din Azure VPN-gateway och den lokala nätverksgatewayen

## <a name="create-a-local-network-gateway"></a>Skapa en lokal nätverksgateway

En lokal nätverksgateway representerar det lokala nätverket. Du kan ange egenskaperna för det lokala nätverket i den lokala nätverksgatewayen, inklusive:

* Offentlig IP-adress för VPN-enheten
* Lokalt adressutrymme
* (Valfritt) BGP-attribut (IP-adress för BGP-peer och AS-nummer)

Skapa en lokal nätverksgateway med kommandot [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Skapa en VPN-anslutning mellan servrar

Därefter skapar du VPN-anslutningen för plats till plats mellan din virtuella nätverksgateway och din VPN-enhet med [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection). Observera att ”-ConnectionType” för VPN för plats till plats är *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Lägg till den valfria egenskapen ”**-EnableBGP $True**” för att aktivera BGP:n för anslutningen om du använder BGP. Det är inaktiverat som standard.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Uppdatera VPN-anslutningens i förväg delade nyckel, BGP och IPsec/IKE-princip

### <a name="view-and-update-your-pre-shared-key"></a>Visa och uppdatera din i förväg delade nyckel

Azures VPN-anslutning mellan servrar använder en i förväg delad nyckel (hemlighet) till att autentisera mellan din lokala VPN-enhet och Azure VPN-gatewayen. Du kan visa och uppdatera den i förväg delade nyckeln för en anslutning med [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) och [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Den i förväg delade nyckeln är en sträng med **utskrivbara ASCII-tecken** med en maximal längd på 128.

Det här kommandot visar den i förväg delade nyckeln för anslutningen:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

Utdata blir ”**Azure@!b2C3**” enligt exemplet ovan. Använd kommandot nedan för att ändra värdet i den förväg delade nyckeln till ”**Azure@!_b2=C3**”:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Aktivera BGP på en VPN-anslutning

Azure VPN-gatewayen har stöd för BGP-protokoll för dynamisk routning. Du kan aktivera BGP på varje enskild anslutning, beroende på om du använder BGP i ditt lokala nätverk och på dina enheter eller ej. Ange följande egenskaper för BGP innan du aktiverar BGP på anslutningen:

* ASN (autonomt systemnummer) för Azure VPN
* ASN för lokal nätverksgateway
* IP-adress för BGP-peer för lokal nätverksgateway

Om du inte har konfigurerat BGP-egenskaperna använder du följande kommandon för att lägga till dessa egenskaper i din VPN-gateway och lokala nätverksgateway: [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) och [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Aktivera BGP med [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

Du kan inaktivera BGP genom att ändra egenskapsvärdet ”-EnableBGP” till **$False**. Se [BGP på Azure VPN-gatewayer](vpn-gateway-bgp-resource-manager-ps.md) för mer detaljerade beskrivningar av BGP på Azure VPN-gatewayer.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Tillämpa en anpassad princip för IPsec/IKE på anslutningen

Du kan tillämpa en valfri IPsec/IKE-princip för att ange den exakta kombinationen av IPsec/IKE:s kryptografiska algoritmer och nyckelstyrkor i anslutningen, i stället för att använda [standardförslagen](vpn-gateway-about-vpn-devices.md#ipsec). Följande exempelskript skapar en annan IPsec/IKE-princip med följande algoritmer och parametrar:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, SA-livslängd 14 400 sekunder och 102 400 000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Se [IPsec/IKE-princip för anslutningar mellan servrar eller mellan virtuella nätverk](vpn-gateway-ipsecikepolicy-rm-powershell.md) för en fullständig lista med algoritmer och instruktioner.

## <a name="add-another-s2s-vpn-connection"></a>Skapa en till VPN-anslutning mellan servrar

Lägg till ytterligare en VPN-anslutning mellan servrar till samma VPN-gateway genom att skapa en annan lokal nätverksgateway och skapa en ny anslutning mellan den nya lokala nätverksgatewayen och VPN-gatewayen. Följ exemplet i den här artikeln.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Det finns nu två VPN-anslutningar mellan servrar till din Azure VPN-gateway.

![MultiSite VPN-anslutningar](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Ta bort en VPN-anslutning mellan servrar

Ta bort en VPN-anslutning mellan servrar med [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Ta bort den lokala nätverksgatewayen om du inte längre behöver den. Du kan inte ta bort en lokal nätverksgateway om det finns andra anslutningar som är kopplade till den.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att skapa och hantera VPN-anslutningar mellan servrar, till exempel att:

> [!div class="checklist"]
> * Skapa en VPN-anslutning mellan servrar
> * Uppdatera anslutningsegenskapen: i förväg delad nyckel, BGP, IPsec/IKE-princip
> * Lägga till flera VPN-anslutningar
> * Ta bort en VPN-anslutning

Gå vidare till följande självstudier för att lära dig mer om anslutningar mellan servrar och mellan virtuella nätverk, samt P2S-anslutningar.

> [!div class="nextstepaction"]
> * [Skapa anslutningar mellan virtuella nätverk](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Skapa P2S-anslutningar](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
