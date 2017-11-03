---
title: "Konfigurera Tvingad tunneltrafik för Azure plats-till-plats-anslutningar: klassiska | Microsoft Docs"
description: "Så här dirigerar eller 'tvinga' alla Internet-bunden trafik tillbaka till den lokala platsen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurera framtvingad tunneling med den klassiska distributionsmodellen

Tvingad tunneling kan du omdirigera eller ”force” alla Internet-bunden trafik tillbaka till din lokala plats via en plats-till-plats VPN-tunnel för kontroll och granskning. Detta är en kritisk säkerhetskraven för de flesta företags IT principer. Utan Tvingad tunneling ska Internet-bunden trafik från din virtuella datorer i Azure alltid passerar från Azure nätverksinfrastruktur direkt ut till Internet, utan att du vill granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information och andra typer av säkerhetsintrång.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Den här artikeln beskriver hur du konfigurerar Tvingad tunneltrafik för virtuella nätverk som skapats med den klassiska distributionsmodellen. Tvingad tunneltrafik kan konfigureras med hjälp av PowerShell inte via portalen. Om du vill konfigurera Tvingad tunneling för Resource Manager-distributionsmodellen Välj klassiska artikel från listan följande:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Krav och överväganden
Tvingad tunneling i Azure konfigureras via virtuella nätverk användardefinierade vägar (UDR). Omdirigera trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. Följande avsnitt innehåller aktuella begränsningen på Routning och vägar för ett Azure Virtual Network:

* Varje undernät för virtuellt nätverk har en inbyggd, system-routningstabell. Routningstabellen för systemet har följande tre grupper av vägar:

  * **Lokal VNet vägar:** direkt till målet virtuella datorer i samma virtuella nätverk.
  * **Lokala vägar:** till Azure VPN-gateway.
  * **Standardvägen:** direkt till Internet. Paket avsedda för de privata IP-adresser som inte omfattas av föregående två vägar tas bort.
* Med lanseringen av användardefinierade vägar, kan du skapa en routningstabell för att lägga till en standardväg och sedan associerar routningstabellen för din VNet-adressundernät för att aktivera Tvingad tunneltrafik på dessa undernät.
* Du måste ange en ”standardwebbplats” bland de lokala mellan lokala platserna anslutna till det virtuella nätverket.
* Tvingad tunneling måste vara kopplad till ett virtuellt nätverk som har en dynamisk routning VPN-gateway (inte en statisk gateway).
* ExpressRoute Tvingad tunneltrafik konfigureras inte via den här mekanismen, men har aktiverats genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner i stället. Finns det [ExpressRoute dokumentation](https://azure.microsoft.com/documentation/services/expressroute/) för mer information.

## <a name="configuration-overview"></a>Konfigurationsöversikt
I följande exempel tunneldata klientdel undernät inte tvingas. Arbetsbelastningar i undernätet Frontend kan fortsätta att acceptera och svara på kundernas önskemål direkt från Internet. Halva nivå och Backend-undernät tvingas tunnel. Alla utgående anslutningar från dessa två undernät till Internet ska tvingas eller omdirigeras till en lokal plats via en S2S VPN-tunnlar.

På så sätt kan du begränsa och inspektera Internetåtkomst från dina virtuella datorer eller molntjänster i Azure, medan du aktivera din flernivåtjänst arkitektur som krävs. Du kan också använda Tvingad tunneling hela virtuella nätverk, om det finns ingen Internet-riktade arbetsbelastningar i ditt virtuella nätverk.

![Forcerade tunnlar](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Konfigurerade virtuella nätverk. 
* Den senaste versionen av Azure PowerShell-cmdlets. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik
Med hjälp av följande procedur kan du ange Tvingad tunneling för ett virtuellt nätverk. Konfigurationssteg motsvarar konfigurationsfilen VNet nätverk.

```
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

I det här exemplet har tre undernät i det virtuella nätverket MultiTier-VNet: 'Klientdel', 'Midtier' och 'Backend-undernät med fyra mellan lokala anslutningar: 'DefaultSiteHQ' och tre filialer. 

Stegen kommer DefaultSiteHQ som standard plats-anslutning för Tvingad tunneltrafik, och konfigurera Midtier och Backend-undernät att använda Tvingad tunneltrafik.

1. Skapa en routningstabell. Använd följande cmdlet för att skapa din routningstabellen.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Lägg till en standardväg i routningstabellen. 

  I följande exempel läggs en standardväg i routningstabellen skapade i steg 1. Observera att endast vägen som stöds är målprefix för ”0.0.0.0/0” till ”VPNGateway” NextHop.

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associera routningstabellen i undernät. 

  När en routningstabell skapas och lägga till en väg, Använd följande exempel för att lägga till eller associera routningstabellen till ett undernät för virtuellt nätverk. Exemplet lägger till vägtabellen ”MyRouteTable” Midtier och Backend-undernät i VNet MultiTier-VNet.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Tilldela en standardplats för Tvingad tunneltrafik. 

  I det föregående steget, cmdlet exempelskript skapas routningstabellen och associerade routningstabellen med två undernät för virtuellt nätverk. Återstående steg är att välja en lokal plats bland flera plats-anslutningar för det virtuella nätverket som standardwebbplatsen eller tunnel.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Ytterligare PowerShell-cmdlets
### <a name="to-delete-a-route-table"></a>Ta bort en routningstabell

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Att visa en routningstabell

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Ta bort en väg från en routningstabell

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Ta bort en väg från ett undernät

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Att visa routningstabellen som är associerad med ett undernät

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Ta bort en standardwebbplats från ett VNet VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```