---
title: 'Anslut ett virtuellt nätverk till flera platser med hjälp av VPN-Gateway och PowerShell: Klassiska | Microsoft Docs'
description: Anslut flera lokala platser till ett klassiskt virtuellt nätverk med hjälp av en VPN-Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994014"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Lägg till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN-gateway-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
>

Den här artikeln visar hur du använder PowerShell för att lägga till anslutningar för plats-till-plats (S2S) till en VPN-gateway som har en befintlig anslutning. Den här typen av anslutning kallas ofta en konfiguration för flera platser ””. Stegen i den här artikeln gäller för virtuella nätverk som skapats med den klassiska distributionsmodellen (även känt som Service Management). De här stegen gäller inte för ExpressRoute/plats-till-plats kan samexistera anslutningskonfigurationer.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar tabellen som nya artiklar och verktyg blir tillgängliga för den här konfigurationen. Om det finns en artikel länkar vi till den direkt från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Om hur du ansluter

Du kan ansluta flera lokala platser till ett enda virtuellt nätverk. Detta är särskilt användbart om man bygger hybridmolnlösningar. Skapa en anslutning till flera platser till din Azure-nätverksgateway liknar skapar andra plats-till-plats-anslutningar. I själva verket kan du kan använda en befintlig Azure VPN-gateway så länge som gatewayen är dynamisk (routningsbaserad).

Om du redan har en statisk gateway som är anslutna till ditt virtuella nätverk kan ändra du gateway-typ till dynamisk utan att behöva återskapa det virtuella nätverket för att kunna hantera flera platser. Innan du ändrar routningstyp som du kontrollerar du att din lokala VPN-gateway stöder ruttbaserad VPN-konfigurationer.

![diagram för flera platser](./media/vpn-gateway-multi-site/multisite.png "multisite")

## <a name="points-to-consider"></a>Några saker att tänka på

**Du kan inte använda portalen för att göra ändringar i det här virtuella nätverket.** Du måste göra ändringar i nätverkskonfigurationsfilen i stället för med hjälp av portalen. Om du gör ändringar i portalen, skrivs de över inställningarna multisite-referens för det här virtuella nätverket.

Du bör upplev bekvämligheten med hjälp av nätverkskonfigurationsfilen när du har slutfört proceduren för flera platser. Om du har flera personer som arbetar på din nätverkskonfiguration, måste du dock att se till att alla känner till den här begränsningen. Detta innebär inte att du inte kan använda portalen alls. Du kan använda den för allt annat, förutom att utföra konfigurationsändringar i det här specifika virtuella nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurationen måste du kontrollera att du har följande:

* Kompatibel VPN-maskinvara för var och en lokal plats. Kontrollera [om VPN-enheter för virtuell nätverksanslutning](vpn-gateway-about-vpn-devices.md) att kontrollera om den enhet som du vill använda är något som är känt att vara kompatibel.
* En extern offentlig IPv4 IP-adress för varje VPN-enhet. IP-adressen får inte finnas bakom en NAT. Detta är krav.
* Du måste installera den senaste versionen av Azure PowerShell-cmdletarna. Kontrollera att du installerar Service Management (SM) version förutom Resource Manager-version. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information.
* Någon som skicklig på att konfigurera din VPN-maskinvara. Du måste ha en mer ingående förståelse av hur du konfigurerar VPN-enheten eller arbeta med någon som.
* IP-adressintervall som du vill använda för ditt virtuella nätverk (om du inte redan har skapat en).
* IP-adressintervall för var och en av de lokala nätverksplatser som du ska kunna ansluta till. Du måste se till att adressintervall för var och en av de lokala nätverksplatser som du vill ansluta till inte överlappar varandra. I annat fall avvisar på portalen eller REST API den konfiguration som laddas upp.<br>Om du har två lokala nätverksplatser att båda innehåller IP-adressintervallet 10.2.3.0/24 och du har ett paket med en mål-adress 10.2.3.3 skulle Azure vet vilken plats som du vill skicka paketet till eftersom överlappar adressintervallen. Om du vill förhindra routningsproblem Azure tillåter inte att du överför en konfigurationsfil som har överlappande områden.

## <a name="1-create-a-site-to-site-vpn"></a>1. Skapa en VPN för plats-till-plats
Om du redan har en plats-till-plats VPN med en dynamisk routningsgateway bra! Du kan fortsätta att [exportera konfigurationsinställningar för virtuellt nätverk](#export). Annars kan du göra följande:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Om du redan har ett virtuellt nätverk för plats-till-plats, men den har en statisk routning (principbaserad)-gateway:
1. Ändra din gatewaytyp till dynamisk routning. En VPN-anslutning för flera platser kräver en dynamisk routning (även kallat routningsbaserad)-gateway. Om du vill ändra gatewaytyp av måste du först ta bort den befintliga gatewayen och sedan skapa en ny.
2. Konfigurera din nya gatewayen och skapa din VPN-tunnel. Instruktioner för mer information finns i [ange SKU- och VPN-typ](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Kontrollera att du anger typen av routning som ”dynamiska”.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Om du inte har ett virtuellt nätverk för plats-till-plats:
1. Skapa ditt virtuella nätverk för plats-till-plats med hjälp av anvisningarna: [Skapa ett virtuellt nätverk med en plats-till-plats-VPN-anslutning](vpn-gateway-site-to-site-create.md).  
2. Konfigurera en dynamisk routningsgateway som använder dessa instruktioner: [Konfigurera en VPN-Gateway](vpn-gateway-configure-vpn-gateway-mp.md). Se till att välja **dynamisk routning** för gatewaytypen.

## <a name="export"></a>2. Exportera nätverkskonfigurationsfilen
Exportera konfigurationsfilen Azure-nätverk genom att köra följande kommando. Du kan ändra platsen för den fil som ska exporteras till en annan plats om det behövs.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Öppna nätverkskonfigurationsfilen
Öppna nätverkskonfigurationsfilen som du hämtade i föregående steg. Använda en xml-redigerare som helst. Filen bör se ut ungefär så här:

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Lägg till flera plats-referenser
När du lägger till eller ta bort platsen Referensinformation ska du göra konfigurationsändringar till ConnectionsToLocalNetwork/LocalNetworkSiteRef. Lägger till en ny lokal plats referens-utlösare på Azure för att skapa en ny tunnel. I exemplet nedan är nätverkskonfigurationen för en enskild plats-anslutning. Spara filen när du är klar med att göra dina ändringar.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Lägg bara till ytterligare ”LocalNetworkSiteRef” rader som visas i exemplet nedan om du vill lägga till ytterligare referenser (skapa en multisite-konfiguration):

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importera nätverkskonfigurationsfilen
Importera nätverkskonfigurationsfilen. När du importerar filen med ändringarna läggs nya tunnlarna. Tunnlarna kommer att använda dynamisk gateway som du skapade tidigare. Du kan använda PowerShell för att importera filen.

## <a name="6-download-keys"></a>6. Hämta nycklar
När din nya tunnlar har lagts till, kan du använda PowerShell-cmdleten ”Get-AzureVNetGatewayKey” för att hämta IPsec/IKE förväg delade nycklar för varje tunnel.

Exempel:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Om du vill kan du också använda den *hämta virtuella nätverk Gateway delad nyckel* REST API för att få de i förväg delade nycklarna.

## <a name="7-verify-your-connections"></a>7. Verifiera dina anslutningar
Kontrollera statusen för flera platser tunnel. När du hämtat nycklarna för varje tunnel, bör du verifiera anslutningar. Använd ”Get-AzureVnetConnection” för att hämta en lista över virtuella nätverk-tunnlar, som visas i exemplet nedan. VNet1 är namnet på det virtuella nätverket.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exempel returnerade:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Nästa steg

Mer information om VPN-gatewayer finns [om VPN gateway](vpn-gateway-about-vpngateways.md).
