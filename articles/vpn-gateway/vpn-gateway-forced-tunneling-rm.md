---
title: Konfigurera Tvingad tunnel trafik för plats-till-plats-anslutningar
description: Omdirigera eller tvinga all Internet-baserad trafik tillbaka till din lokala plats.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e9444291c40ef504a674ee18351ba581695d1dd3
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394526"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurera framtvingad tunneling med distributionsmodellen Azure Resource Manager

Med tvingad tunneltrafik kan du omdirigera eller ”tvinga” all Internetbunden trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Detta är ett kritiskt säkerhets krav för de flesta företagets IT-principer. Utan Tvingad tunnel trafik passerar Internet-bindningen från dina virtuella datorer i Azure alltid från Azures nätverks infrastruktur direkt till Internet, utan alternativet att låta dig inspektera eller granska trafiken. Otillåten Internet åtkomst kan potentiellt leda till information som avslöjas eller andra typer av säkerhets överträdelser.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Den här artikeln beskriver hur du konfigurerar Tvingad tunnel trafik för virtuella nätverk som skapats med Resource Manager-distributions modellen. Tvingad tunnel trafik kan konfigureras med hjälp av PowerShell, inte via portalen. Om du vill konfigurera Tvingad tunnel trafik för den klassiska distributions modellen väljer du klassisk artikel i följande listruta:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Om Tvingad tunnel trafik

Följande diagram illustrerar hur Tvingad tunnel trafik fungerar. 

![Tvingad tunnel trafik](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

I exemplet ovan tvingas inte frontend-undernätet att tunnlas. Arbets belastningarna i klient delens undernät kan fortsätta att acceptera och svara på kund förfrågningar direkt från Internet. Mellanlagrings-och backend-undernät framtvingas med tunnel. Utgående anslutningar från dessa två undernät till Internet tvingas eller omdirigeras tillbaka till en lokal plats via en av S2S VPN-tunnlarna.

På så sätt kan du begränsa och kontrol lera Internet åtkomst från dina virtuella datorer eller moln tjänster i Azure, samtidigt som du fortsätter att aktivera din tjänst arkitektur för flera nivåer som krävs. Om det inte finns några Internet-riktade arbets belastningar i dina virtuella nätverk kan du även använda Tvingad tunnel trafik till hela virtuella nätverk.

## <a name="requirements-and-considerations"></a>Krav och överväganden

Tvingad tunnel trafik i Azure konfigureras via användardefinierade vägar för virtuella nätverk. Att omdirigera trafik till en lokal plats uttrycks som en standard väg till Azure VPN-gatewayen. Mer information om användardefinierade Routning och virtuella nätverk finns i [användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md).

* Varje undernät för virtuellt nätverk har en inbyggd system routningstabell. Tabellen system routning har följande tre grupper av vägar:
  
  * **Lokala VNet-vägar:** Direkt till de virtuella mål datorerna i samma virtuella nätverk.
  * **Lokala vägar:** Till Azure VPN-gatewayen.
  * **Standard väg:** Direkt till Internet. Paket som är avsedda för privata IP-adresser som inte omfattas av de föregående två vägarna ignoreras.
* Den här proceduren använder användardefinierade vägar (UDR) för att skapa en routningstabell för att lägga till en standard väg och sedan associera routningstabellen till dina VNet-undernät för att aktivera Tvingad tunnel trafik på dessa undernät.
* Tvingad tunnel trafik måste vara kopplad till ett VNet som har en Route-baserad VPN-gateway. Du måste ange en "standard webbplats" bland de lokala lokala platserna som är anslutna till det virtuella nätverket. Dessutom måste den lokala VPN-enheten konfigureras med 0.0.0.0/0 som trafik väljare. 
* ExpressRoute-Tvingad tunnel trafik har inte kon figurer ATS via den här mekanismen, utan är i stället aktive rad genom att annonsera en standard väg via ExpressRoute BGP-peering-sessioner. Mer information finns i ExpressRoute- [dokumentationen](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Översikt över konfiguration

Med följande procedur kan du skapa en resurs grupp och ett VNet. Därefter skapar du en VPN-gateway och konfigurerar Tvingad tunnel trafik. I den här proceduren har det virtuella nätverket "multitier-VNet" tre undernät: ' frontend ', ' midtier ' och ' backend ', med fyra anslutningar mellan olika platser: ' DefaultSiteHQ ' och tre grenar.

Procedur stegen anger "DefaultSiteHQ" som standard plats anslutning för Tvingad tunnel trafik och konfigurerar undernätarna "midtier" och "backend" så att de använder Tvingad tunnel trafik.

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Du måste installera den senaste versionen av PowerShell-cmdletar. Annars kan du få verifierings fel när du kör några av cmdletarna.
>
>

### <a name="to-log-in"></a>Logga in

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik

> [!NOTE]
> Du kan se varningar om att den här cmdletens typ av utdata kommer att ändras i framtida versioner. Detta är ett förväntat beteende och du kan ignorera dessa varningar på ett säkert sätt.
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
3. Skapa de lokala Nätverksgatewayen.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Skapa väg tabellen och flödes regeln.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Koppla routningstabellen till midtier-och backend-undernät.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Skapa den virtuella Nätverksgatewayen. Det här steget tar lite tid att slutföra, ibland 45 minuter eller mer, eftersom du skapar och konfigurerar gatewayen. Om du ser ValidateSet-fel angående GatewaySKU-värdet kontrollerar du att du har installerat den [senaste versionen av PowerShell-cmdletarna](#before). Den senaste versionen av PowerShell-cmdletarna innehåller de nya validerade värdena för de senaste Gateway-SKU: erna.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Tilldela en standard plats till den virtuella Nätverksgatewayen. Parametern **-GatewayDefaultSite** är den cmdlet-parameter som tillåter att konfigurationen för tvingande routning fungerar, så du bör vara noga med att konfigurera den här inställningen korrekt. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Upprätta plats-till-plats-VPN-anslutningar.

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
