---
title: Konfigurera tvingad tunnel för anslutningar från plats till plats
description: Så här omdirigerar eller tvingar du tillbaka all Internet-bunden trafik till din lokala plats.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244633"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurera framtvingad tunneling med distributionsmodellen Azure Resource Manager

Med tvingad tunneltrafik kan du omdirigera eller ”tvinga” all Internetbunden trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Detta är ett kritiskt säkerhetskrav för de flesta FÖRETAGETS IT-principer. Utan påtvingad tunneltrafik passerar Internet-bunden trafik från dina virtuella datorer i Azure alltid från Azure-nätverksinfrastruktur direkt ut till Internet, utan möjlighet att inspektera eller granska trafiken. Obehörig åtkomst till Internet kan potentiellt leda till utlämnande av information eller andra typer av säkerhetsöverträdelser.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

I den här artikeln går du igenom konfigurationen av tvångstunneler för virtuella nätverk som skapats med hjälp av resurshanterarens distributionsmodell. Tvingad tunnel kan konfigureras med PowerShell, inte via portalen. Om du vill konfigurera påtvingad tunnel för den klassiska distributionsmodellen väljer du klassisk artikel i följande listruta:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Om påtvingad tunnel

Följande diagram illustrerar hur påtvingad tunnelning fungerar. 

![Påtvingad tunnel](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

I exemplet ovan tvingas inte Frontend-undernätet att tunneleras. Arbetsbelastningarna i Frontend-undernätet kan fortsätta att acceptera och svara på kundförfrågningar från Internet direkt. Undernäten Mid-tier och Backend tvingas tunnel. Alla utgående anslutningar från dessa två undernät till Internet kommer att tvingas eller omdirigeras tillbaka till en lokal plats via en av S2S VPN-tunnlarna.

På så sätt kan du begränsa och granska Internet-åtkomst från dina virtuella datorer eller molntjänster i Azure, samtidigt som du fortsätter att aktivera din tjänstarkitektur på flera nivåer som krävs. Om det inte finns några Internet-arbetsbelastningar i dina virtuella nätverk kan du också använda tvångstunneler på hela virtuella nätverk.

## <a name="requirements-and-considerations"></a>Krav och överväganden

Tvingad tunnel i Azure konfigureras via användardefinierade vägar för virtuella nätverk. Omdirigera trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. Mer information om användardefinierad routning och virtuella nätverk finns i [Användardefinierade vägar och IP-vidarebefordran](../virtual-network/virtual-networks-udr-overview.md).

* Varje virtuellt nätverksundernät har en inbyggd systemroutningstabell. Systemdirigeringstabellen har följande tre grupper av vägar:
  
  * **Lokala virtuella nätverksvägar:** Direkt till virtuella mål-datorer i samma virtuella nätverk.
  * **Lokala rutter:** Till Azure VPN-gatewayen.
  * **Standardväg:** Direkt till Internet. Paket som är avsedda för de privata IP-adresser som inte omfattas av de två föregående rutterna tas bort.
* Den här proceduren använder användardefinierade vägar (UDR) för att skapa en routningstabell för att lägga till en standardväg och associerar sedan routningstabellen till dina VNet-undernät för att aktivera påtvingad tunnelning i dessa undernät.
* Tvingad tunnelning måste associeras med ett virtuella nätverk som har en ruttbaserad VPN-gateway. Du måste ange en "standardplats" bland de lokala platser som är anslutna till det virtuella nätverket. Dessutom måste den lokala VPN-enheten konfigureras med 0.0.0.0/0 som trafikväljare. 
* ExpressRoute påtvingad tunnelning är inte konfigurerad via den här mekanismen, utan aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peering-sessionerna. Mer information finns i [ExpressRoute-dokumentationen](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Översikt över konfiguration

Följande procedur hjälper dig att skapa en resursgrupp och ett virtuella nätverk. Du skapar sedan en VPN-gateway och konfigurerar tvingande tunnel. I det här förfarandet har det virtuella nätverket MultiTier-VNet tre undernät: "Frontend", "Midtier" och "Backend", med fyra anslutningsövergripande anslutningar: "DefaultSiteHQ" och tre grenar.

Proceduren steg som "DefaultSiteHQ" som standard platsanslutning för påtvingad tunnel, och konfigurera "Midtier" och "Backend" undernät att använda påtvingad tunnel.

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Det krävs installation av den senaste versionen av PowerShell-cmdlets. Annars kan du få valideringsfel när du kör några av cmdleterna.
>
>

### <a name="to-log-in"></a>Så här loggar du in

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik

> [!NOTE]
> Du kan se varningar som säger "Utdataobjekttypen för den här cmdleten kommer att ändras i en framtida version". Detta är förväntat beteende och du kan säkert ignorera dessa varningar.
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
4. Skapa flödestabellen och flödesregeln.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Associera flödestabellen med undernäten Mellantier och Backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Skapa den virtuella nätverksgatewayen. Det här steget tar lite tid att slutföra, ibland 45 minuter eller mer, eftersom du skapar och konfigurerar gatewayen. Om du ser ValidateSet-fel avseende GatewaySKU-värdet kontrollerar du att du har installerat den [senaste versionen av PowerShell-cmdlets](#before). Den senaste versionen av PowerShell-cmdlets innehåller de nya validerade värdena för de senaste gateway-SKU:erna.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Tilldela en standardplats till den virtuella nätverksgatewayen. **-GatewayDefaultSite** är cmdlet-parametern som gör att den påtvingade routningskonfigurationen fungerar, så var noga med att konfigurera den här inställningen på rätt sätt. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Upprätta VPN-anslutningar för plats till plats.

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
