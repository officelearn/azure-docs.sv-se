---
title: 'Konfigurera Tvingad tunneltrafik för Azure plats-till-plats-anslutningar: klassisk | Microsoft Docs'
description: Så här att omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 71a8077f2423dd170d08d540edd307c08ed886cc
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52165526"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurera framtvingad tunneling med den klassiska distributionsmodellen

Tvingad tunneltrafik kan du omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Det här är en kritisk säkerhetskrav för de flesta företag IT principer. Utan Tvingad tunneltrafik Internet-bunden trafik från dina virtuella datorer i Azure alltid går igenom från Azure nätverksinfrastrukturen direkt ut till Internet, utan alternativet så att du kan granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information eller andra typer av säkerhetsproblem.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Den här artikeln beskriver hur du konfigurerar Tvingad tunneltrafik för virtuella nätverk som skapats med den klassiska distributionsmodellen. Tvingad tunneltrafik kan konfigureras med hjälp av PowerShell, inte via portalen. Om du vill konfigurera Tvingad tunneltrafik för distributionsmodellen i Resource Manager väljer du klassiska artikeln från listan följande:

> [!div class="op_single_selector"]
> * [PowerShell – Klassisk](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Krav och överväganden
Tvingad tunneltrafik i Azure konfigureras via virtuella nätverk användardefinierade vägar (UDR). Omdirigerar trafik till en lokal plats uttrycks som en standardväg till Azure VPN-gatewayen. I följande avsnitt visas den aktuella begränsningen Routning och vägar för ett virtuellt Azure-nätverk:

* Varje virtuellt nätverksundernät har en inbyggd, system-routningstabell. Routningstabellen system har följande tre grupper av vägar:

  * **Lokala virtuella nätverket vägar:** direkt till målets virtuella datorer i samma virtuella nätverk.
  * **Lokala vägar:** till Azure VPN-gateway.
  * **Standardvägen:** direkt till Internet. Paket som är avsedd för de privata IP-adresser som inte omfattas av de föregående två vägarna tas bort.
* Med lanseringen av användardefinierade vägar kan du skapa en routningstabell för att lägga till en standardväg och sedan associera routningstabellen till din VNet-undernät för att aktivera Tvingad tunneltrafik på dessa undernät.
* Du måste ange en ”standardwebbplats” mellan de över flera lokala platserna anslutna till det virtuella nätverket.
* Tvingad tunneltrafik måste vara associerad med ett virtuellt nätverk som har en VPN-gateway för dynamisk routning (inte en statisk gateway).
* ExpressRoute Tvingad tunneltrafik är inte konfigurerad via den här mekanismen, men i stället aktiveras genom att annonsera en standardväg via ExpressRoute BGP-peeringsessioner. Finns det [dokumentation om ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) för mer information.

## <a name="configuration-overview"></a>Konfigurationsöversikt
I följande exempel tunneltrafik klientdelen undernät inte Tvingad. Arbetsbelastningar i undernätet på klientsidan kan fortsätta att godkänna och svara på kundernas önskemål direkt från Internet. Medelnivån och Backend-undernät är Tvingad tunneltrafik. Alla utgående anslutningar från de här två undernät till Internet ska tvingas eller omdirigeras tillbaka till en lokal plats via en S2S VPN-tunnlarna.

På så sätt kan du begränsa och granska Internetåtkomst från dina virtuella datorer eller molntjänster i Azure, medan du aktivera din tjänst med flera nivåer arkitektur som krävs. Du kan också använda Tvingad tunneltrafik hela virtuella nätverk, om det finns ingen Internet-riktade arbetsbelastningar i ditt virtuella nätverk.

![Forcerade tunnlar](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Konfigurerade virtuella nätverk. 
* Den senaste versionen av Azure PowerShell-cmdlets. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik
Följande procedur kan du ange Tvingad tunneltrafik för ett virtuellt nätverk. Konfigurationsstegen motsvarar nätverkskonfigurationsfilen VNet.

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

I det här exemplet har tre undernät i det virtuella nätverket MultiTier-VNet: ”Frontend”, ”Midtier” och ”serverdel” undernät med fyra anslutningar på flera platser: 'DefaultSiteHQ' och tre grenar. 

Stegen kommer DefaultSiteHQ som standard plats-anslutning för Tvingad tunneltrafik, och konfigurera Midtier och Backend-undernät att använda Tvingad tunneltrafik.

1. Skapa en routningstabell. Använd följande cmdlet för att skapa din routningstabell.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Lägg till en standardväg till i routningstabellen. 

  I följande exempel läggs en standardväg till i routningstabellen som skapade i steg 1. Observera att den enda vägen som stöds är målprefix för ”0.0.0.0/0” till ”VPNGateway” NextHop.

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Associera routningstabellen för undernäten. 

  När en routningstabell har skapats och lagts till en väg, Använd följande exempel för att lägga till eller associera routningstabellen till ett VNet-undernät. I exemplet lägger till i routningstabellen ”MyRouteTable” Midtier och Backend-undernät i VNet MultiTier-VNet.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Tilldela en standardplats för Tvingad tunneltrafik. 

  I det föregående steget, exempelskript för cmdleten skapat routningstabellen och tillhörande routningstabellen till två VNet-undernät. Återstående steg är att välja en lokal plats bland flera plats-anslutningar för det virtuella nätverket som standardwebbplatsen eller tunnel.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Ytterligare PowerShell-cmdletar
### <a name="to-delete-a-route-table"></a>Att ta bort en routningstabell

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Visa en lista över en routningstabell

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Att ta bort en väg från en routningstabell

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Ta bort en väg från ett undernät

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Visa en lista över routningstabellen som är associerade med ett undernät

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Ta bort en standardwebbplats från ett VNet VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```