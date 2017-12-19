---
title: "Konfigurera Tvingad tunneltrafik för Azure plats-till-plats-anslutningar: Resource Manager | Microsoft Docs"
description: "Så här dirigerar eller 'tvinga' alla Internet-bunden trafik tillbaka till den lokala platsen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: cherylmc
ms.openlocfilehash: cc8a3e7f2a907b1eea4ecf39df2b291b0fb8b207
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurera framtvingad tunneling med distributionsmodellen Azure Resource Manager

Tvingad tunneling kan du omdirigera eller ”force” alla Internet-bunden trafik tillbaka till din lokala plats via en plats-till-plats VPN-tunnel för kontroll och granskning. Detta är en kritisk säkerhetskraven för de flesta företags IT principer. Utan Tvingad tunneltrafik, Internet-bunden trafik från din virtuella datorer i Azure alltid går från Azure nätverksinfrastruktur direkt ut till Internet, utan att du vill granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information och andra typer av säkerhetsintrång.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Den här artikeln beskriver hur du konfigurerar Tvingad tunneltrafik för virtuella nätverk som skapats med hjälp av Resource Manager-distributionsmodellen. Tvingad tunneltrafik kan konfigureras med hjälp av PowerShell inte via portalen. Om du vill konfigurera Tvingad tunneling för den klassiska distributionsmodellen Välj klassiska artikel från listan följande:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Om Tvingad tunneltrafik

Följande diagram illustrerar hur Tvingad tunneltrafik fungerar. 

![Forcerade tunnlar](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

I exemplet ovan tunneldata klientdel undernät inte tvingas. Arbetsbelastningar i undernätet Frontend kan fortsätta att acceptera och svara på kundernas önskemål direkt från Internet. Halva nivå och Backend-undernät tvingas tunnel. Alla utgående anslutningar från dessa två undernät till Internet ska tvingas eller omdirigeras till en lokal plats via en S2S VPN-tunnlar.

På så sätt kan du begränsa och inspektera Internetåtkomst från dina virtuella datorer eller molntjänster i Azure, medan du aktivera din flernivåtjänst arkitektur som krävs. Om det finns ingen Internet-riktade arbetsbelastningar i dina virtuella nätverk, kan du också använda Tvingad tunneling till hela virtuella nätverk.

## <a name="requirements-and-considerations"></a>Krav och överväganden

Tvingad tunneling i Azure konfigureras via användardefinierade vägar för virtuellt nätverk. Omdirigera trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. Mer information om användardefinierade Routning och virtuella nätverk finns [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).

* Varje undernät för virtuellt nätverk har en inbyggd, system-routningstabell. Routningstabellen för systemet har följande tre grupper av vägar:
  
  * **Lokal VNet vägar:** direkt till målet virtuella datorer i samma virtuella nätverk.
  * **Lokala vägar:** till Azure VPN-gateway.
  * **Standardvägen:** direkt till Internet. Paket avsedda för de privata IP-adresser som inte omfattas av föregående två vägar tas bort.
* Den här proceduren använder användardefinierade vägar (UDR) för att skapa en routningstabell för att lägga till en standardväg och sedan associera routningstabellen för din VNet-adressundernät för att aktivera Tvingad tunneltrafik på dessa undernät.
* Tvingad tunneling måste vara kopplad till ett virtuellt nätverk som har en ruttbaserad VPN-gateway. Du måste ange en ”standardwebbplats” bland de lokala mellan lokala platserna anslutna till det virtuella nätverket. Lokala VPN-enheten måste också konfigureras med 0.0.0.0/0 som trafik väljare. 
* ExpressRoute Tvingad tunneltrafik konfigureras inte via den här mekanismen, men har aktiverats genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner i stället. Mer information finns i [ExpressRoute dokumentation](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Konfigurationsöversikt

Följande procedur kan du skapa en resursgrupp och ett VNet. Sedan skapar en VPN-gateway och konfigurera Tvingad tunneltrafik. I den här proceduren har tre undernät i det virtuella nätverket MultiTier-VNet: 'Klientdel', 'Midtier' och 'Backend' med fyra mellan lokala anslutningar: 'DefaultSiteHQ' och tre filialer.

Anvisningarna för proceduren 'DefaultSiteHQ' som standard plats-anslutning för Tvingad tunneltrafik, och konfigurera Midtier och 'Backend-undernät att använda Tvingad tunneltrafik.

## <a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Installera den senaste versionen av PowerShell-cmdlets krävs. Annars får valideringsfel när du kör några av cmdletarna.
>
>

### <a name="to-log-in"></a>Logga in

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik

> [!NOTE]
> Varningar som säger ”objekttypen utdata för denna cmdlet kommer att ändras i framtida versioner” kan visas. Detta är förväntat och du kan ignorera dessa varningar.
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
4. Skapa routningstabellen och väg regeln.

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
6. Skapa en Gateway med en standardwebbplats. Det här steget tar en stund att slutföra ibland 45 minuter eller mer, eftersom du skapar och konfigurerar en gateway.<br> Den **- GatewayDefaultSite** är cmdlet-parameter som tillåter framtvingad routningskonfiguration att fungera, så var noga med för att konfigurera den här inställningen korrekt. Om du ser ValidateSet fel om värdet GatewaySKU kontrollerar du att du har installerat den [senaste versionen av PowerShell-cmdlets](#before). Den senaste versionen av PowerShell-cmdlets innehåller nya godkända värden för den senaste Gateway-SKU: er.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -GatewayDefaultSite $lng1 -EnableBgp $false
  ```
7. Upprätta plats-till-plats VPN-anslutningar.

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
