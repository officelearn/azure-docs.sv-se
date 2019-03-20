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
ms.openlocfilehash: b4d9a469e46d964055d9459901ebdb9c6d04cf24
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078354"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurera framtvingad tunneling med distributionsmodellen Azure Resource Manager

Tvingad tunneltrafik kan du omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Det här är en kritisk säkerhetskrav för de flesta företag IT principer. Utan Tvingad tunneltrafik, Internet-bunden trafik från dina virtuella datorer i Azure alltid går från Azure nätverksinfrastrukturen direkt ut till Internet, utan alternativet så att du kan granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information eller andra typer av säkerhetsproblem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

## <a name="configuration-overview"></a>Översikt över konfiguration

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
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Skapa ett virtuellt nätverk och ange undernät.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Skapa de lokala nätverksgatewayerna.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Skapa routningstabell och dirigera regeln.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Associera routningstabellen till Midtier och Backend-undernät.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Skapa den virtuella nätverksgatewayen. Det här steget tar lite tid att slutföra ibland 45 minuter eller mer, eftersom du skapar och konfigurerar gatewayen. Om du ser ValidateSet fel om värdet för GatewaySKU, kontrollerar du att du har installerat den [senaste versionen av PowerShell-cmdletar](#before). Den senaste versionen av PowerShell-cmdletar innehåller de nya verifierade värdena för den senaste Gateway-SKU: er.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Tilldela en standardwebbplats till den virtuella nätverksgatewayen. Den **- GatewayDefaultSite** är cmdlet-parameter som tillåter Tvingad routningskonfiguration arbetar, så var noga med för att konfigurera den här inställningen korrekt. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Upprätta VPN för plats-till-plats-anslutningar.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
