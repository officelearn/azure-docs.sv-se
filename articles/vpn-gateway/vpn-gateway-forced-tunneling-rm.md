---
title: 'Konfigurera Tvingad tunneltrafik för Azure plats-till-plats-anslutningar: Resource Manager | Microsoft Docs'
description: Så här att omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 21004c29f1baf0346cd83d8483ff1862a98fc845
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506472"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurera framtvingad tunneling med distributionsmodellen Azure Resource Manager

Tvingad tunneltrafik kan du omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Det här är en kritisk säkerhetskrav för de flesta företag IT principer. Utan Tvingad tunneltrafik, Internet-bunden trafik från dina virtuella datorer i Azure alltid går från Azure nätverksinfrastrukturen direkt ut till Internet, utan alternativet så att du kan granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information eller andra typer av säkerhetsproblem.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Den här artikeln beskriver hur du konfigurerar Tvingad tunneltrafik för virtuella nätverk som skapats med hjälp av Resource Manager-distributionsmodellen. Tvingad tunneltrafik kan konfigureras med hjälp av PowerShell, inte via portalen. Om du vill konfigurera Tvingad tunneltrafik för den klassiska distributionsmodellen, väljer du klassiska artikeln från listan följande:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Om forcerade tunnlar

Följande diagram illustrerar hur Tvingad tunneltrafik fungerar. 

![Forcerade tunnlar](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

I exemplet ovan tunneltrafik klientdelen undernät inte Tvingad. Arbetsbelastningar i undernätet på klientsidan kan fortsätta att godkänna och svara på kundernas önskemål direkt från Internet. Medelnivån och Backend-undernät är Tvingad tunneltrafik. Alla utgående anslutningar från de här två undernät till Internet ska tvingas eller omdirigeras tillbaka till en lokal plats via en S2S VPN-tunnlarna.

På så sätt kan du begränsa och granska Internetåtkomst från dina virtuella datorer eller molntjänster i Azure, medan du aktivera din tjänst med flera nivåer arkitektur som krävs. Om det finns ingen Internet-riktade arbetsbelastningar i ditt virtuella nätverk kan kan du även använda Tvingad tunneltrafik till hela virtuella nätverk.

## <a name="requirements-and-considerations"></a>Krav och överväganden

Tvingad tunneltrafik i Azure konfigureras via användardefinierade vägar i virtuella nätverk. Omdirigerar trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. Mer information om användardefinierad Routning och virtuella nätverk finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).

* Varje virtuellt nätverksundernät har en inbyggd, system-routningstabell. Routningstabellen system har följande tre grupper av vägar:
  
  * **Lokala virtuella nätverket vägar:** Direkt till målets virtuella datorer i samma virtuella nätverk.
  * **Lokala vägar:** Azure VPN-gatewayen.
  * **Standardvägen:** Direkt till Internet. Ignoreras paket som är avsedd för de privata IP-adresser som inte omfattas av de föregående två vägarna.
* Den här proceduren använder användardefinierade vägar (UDR) för att skapa en routningstabell för att lägga till en standardväg och sedan associera routningstabellen till din VNet-undernät för att aktivera Tvingad tunneltrafik på dessa undernät.
* Tvingad tunneltrafik måste vara associerad med ett virtuellt nätverk som har en routningsbaserad VPN-gateway. Du måste ange en ”standardwebbplats” mellan de över flera lokala platserna anslutna till det virtuella nätverket. Den lokala VPN-enheten måste också konfigureras med 0.0.0.0/0 som trafikväljare. 
* ExpressRoute Tvingad tunneltrafik är inte konfigurerad via den här mekanismen, men i stället aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner. Mer information finns i den [dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Konfigurationsöversikt

Följande procedur kan du skapa en resursgrupp och ett virtuellt nätverk. Du sedan skapa en VPN-gateway och konfigurera Tvingad tunneltrafik. I den här proceduren har tre undernät i det virtuella nätverket MultiTier-VNet: ”Frontend”, ”Midtier” och ”serverdel”, med fyra anslutningar mellan olika platser: 'DefaultSiteHQ' och tre grenar.

Stegen i proceduren ”DefaultSiteHQ” som standard plats-anslutning för Tvingad tunneltrafik, och konfigurera Midtier och ”Backend”-undernät att använda Tvingad tunneltrafik.

## <a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Installera den senaste versionen av PowerShell-cmdletar krävs. I annat fall får verifieringsfel när du kör några av cmdletarna.
>
>

### <a name="to-log-in"></a>Logga in

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik

> [!NOTE]
> Varningar som säger ”objekttypen utdata från denna cmdlet kommer att ändras i kommande versioner” kan visas. Detta är förväntat och du kan ignorera dessa varningar.
>
>


1. Skapa en resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Skapa ett virtuellt nätverk och ange undernät.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Skapa de lokala nätverksgatewayerna.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Skapa routningstabell och dirigera regeln.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Associera routningstabellen till Midtier och Backend-undernät.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Skapa den virtuella nätverksgatewayen. Det här steget tar lite tid att slutföra ibland 45 minuter eller mer, eftersom du skapar och konfigurerar gatewayen. Om du ser ValidateSet fel om värdet för GatewaySKU, kontrollerar du att du har installerat den [senaste versionen av PowerShell-cmdletar](#before). Den senaste versionen av PowerShell-cmdletar innehåller de nya verifierade värdena för den senaste Gateway-SKU: er.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Tilldela en standardwebbplats till den virtuella nätverksgatewayen. Den **- GatewayDefaultSite** är cmdlet-parameter som tillåter Tvingad routningskonfiguration arbetar, så var noga med för att konfigurera den här inställningen korrekt. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Upprätta VPN för plats-till-plats-anslutningar.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
