---
title: 'Azure VPN Gateway: Konfigurera tvingande tunnel - Anslutningar från plats till plats: klassisk'
description: Så här omdirigerar eller tvingar du tillbaka all Internet-bunden trafik till din lokala plats.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201585"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurera framtvingad tunneling med den klassiska distributionsmodellen

Med tvingad tunneltrafik kan du omdirigera eller ”tvinga” all Internetbunden trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Detta är ett kritiskt säkerhetskrav för de flesta FÖRETAGETS IT-principer. Utan påtvingad tunneltrafik kommer Internet-bunden trafik från dina virtuella datorer i Azure alltid att gå från Azure-nätverksinfrastruktur direkt ut till Internet, utan möjlighet att inspektera eller granska trafiken. Obehörig åtkomst till Internet kan potentiellt leda till utlämnande av information eller andra typer av säkerhetsöverträdelser.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

I den här artikeln går du igenom konfigurationen av tvångstunneler för virtuella nätverk som skapats med den klassiska distributionsmodellen. Tvingad tunnel kan konfigureras med PowerShell, inte via portalen. Om du vill konfigurera påtvingad tunnel för resurshanterarens distributionsmodell väljer du Resurshanteraren i följande listruta:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Krav och överväganden
Tvingad tunnel i Azure konfigureras via virtuella nätverksanvändardefinierade vägar (UDR). Omdirigera trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. I följande avsnitt visas den aktuella begränsningen av routningstabellen och routningsvägarna för ett virtuellt Azure-nätverk:

* Varje virtuellt nätverksundernät har en inbyggd systemroutningstabell. Systemdirigeringstabellen har följande tre grupper av vägar:

  * **Lokala virtuella nätverksvägar:** Direkt till virtuella mål-datorer i samma virtuella nätverk.
  * **Lokala rutter:** Till Azure VPN-gatewayen.
  * **Standardväg:** Direkt till Internet. Paket som är avsedda för de privata IP-adresser som inte omfattas av de två föregående rutterna kommer att tas bort.
* Med lanseringen av användardefinierade vägar kan du skapa en routningstabell för att lägga till en standardväg och sedan associera routningstabellen till dina VNet-undernät för att aktivera påtvingad tunnelning i dessa undernät.
* Du måste ange en "standardplats" bland de lokala platser som är anslutna till det virtuella nätverket.
* Tvingad tunnelning måste associeras med ett VNet som har en dynamisk routning VPN-gateway (inte en statisk gateway).
* ExpressRoute påtvingad tunnelning är inte konfigurerad via den här mekanismen, utan aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peering-sessionerna. Mer information finns i [ExpressRoute-dokumentationen.](https://azure.microsoft.com/documentation/services/expressroute/)

## <a name="configuration-overview"></a>Översikt över konfiguration
I följande exempel tvingas inte Frontend-undernätet att tunneleras. Arbetsbelastningarna i Frontend-undernätet kan fortsätta att acceptera och svara på kundförfrågningar från Internet direkt. Undernäten Mid-tier och Backend tvingas tunnel. Alla utgående anslutningar från dessa två undernät till Internet kommer att tvingas eller omdirigeras tillbaka till en lokal plats via en av S2S VPN-tunnlarna.

På så sätt kan du begränsa och granska Internet-åtkomst från dina virtuella datorer eller molntjänster i Azure, samtidigt som du fortsätter att aktivera din tjänstarkitektur på flera nivåer som krävs. Du kan också använda tvångstunneler på hela virtuella nätverk om det inte finns några Internet-arbetsbelastningar i dina virtuella nätverk.

![Påtvingad tunnel](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Ett konfigurerat virtuellt nätverk. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Så här loggar du in

1. Öppna PowerShell-konsolen med förhöjda rättigheter. Om du vill växla till tjänsthantering använder du det här kommandot:

   ```powershell
   azure config mode asm
   ```
2. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik
Följande procedur hjälper dig att ange tvångstunneler för ett virtuellt nätverk. Konfigurationsstegen motsvarar konfigurationsfilen för VNet-nätverk.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

I det här exemplet har det virtuella nätverket MultiTier-VNet tre undernät: "Frontend", "Midtier" och "Backend"-undernät, med fyra korslokalanslutningar: "DefaultSiteHQ" och tre grenar. 

Stegen kommer att ställa in "DefaultSiteHQ" som standardplatsanslutning för påtvingad tunnelning och konfigurerar undernäten Mellantier och Serverdel för att använda forcerad tunnel.

1. Skapa en routningstabell. Använd följande cmdlet för att skapa din rutttabell.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Lägg till en standardväg i routningstabellen. 

   I följande exempel läggs en standardväg till operationsföljdstabellen som skapats i steg 1. Observera att den enda vägen som stöds är målprefixet "0.0.0.0/0" till "VPNGateway" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Associera routningstabellen med undernäten. 

   När en routningstabell har skapats och en väg har lagts till använder du följande exempel för att lägga till eller associera flödestabellen till ett VNet-undernät. I exemplet läggs vägtabellen "MyRouteTable" till undernäten Mellan och bakåt i VNet MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Tilldela en standardplats för påtvingad tunnel. 

   I föregående steg skapade exempel-cmdlet-skripten routningstabellen och associerade flödestabellen till två av VNet-undernäten. Det återstående steget är att välja en lokal plats bland flera platsanslutningar i det virtuella nätverket som standardplats eller tunnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Ytterligare PowerShell-cmdlets
### <a name="to-delete-a-route-table"></a>Så här tar du bort en flödestabell

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Så här listar du en rutttabell

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Så här tar du bort en rutt från en flödestabell

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Så här tar du bort en väg från ett undernät

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Så här listar du flödestabellen som är associerad med ett undernät

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Så här tar du bort en standardplats från en VPN-gateway för VNet

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
