---
title: 'Ansluta ett virtuella nätverk till flera platser med VPN Gateway: Classic'
description: Anslut flera lokala lokala lokala webbplatser till ett klassiskt virtuellt nätverk med hjälp av en VPN-gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198111"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Lägga till en plats-till-plats-anslutning till ett virtuella nätverk med en befintlig VPN-gatewayanslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
>

I den här artikeln får du hjälp med PowerShell för att lägga till S2S-anslutningar (Site to-Site) till en VPN-gateway som har en befintlig anslutning. Den här typen av anslutning kallas ofta för en konfiguration med flera webbplatser. Stegen i den här artikeln gäller virtuella nätverk som skapats med den klassiska distributionsmodellen (kallas även Service Management). De här stegen gäller inte för expressroute-/plats-till-plats-samtidiga anslutningskonfigurationer.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar den här tabellen när nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När en artikel är tillgänglig länkar vi direkt till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Om att ansluta

Du kan ansluta flera lokala platser till ett enda virtuellt nätverk. Detta är särskilt attraktivt för att bygga hybridmolnlösningar. Att skapa en anslutning på flera webbplatser till din virtuella Azure-nätverksgateway påminner om att skapa andra anslutningar från plats till plats. Faktum är att du kan använda en befintlig Azure VPN-gateway, så länge gatewayen är dynamisk (ruttbaserad).

Om du redan har en statisk gateway ansluten till det virtuella nätverket kan du ändra gatewaytypen till dynamisk utan att behöva återskapa det virtuella nätverket för att rymma flera webbplatser. Innan du ändrar routningstypen kontrollerar du att din lokala VPN-gateway stöder ruttbaserade VPN-konfigurationer.

![diagram på flera webbplatser](./media/vpn-gateway-multi-site/multisite.png "flera webbplatser")

## <a name="points-to-consider"></a>Några saker att tänka på

**Du kan inte använda portalen för att göra ändringar i det här virtuella nätverket.** Du måste göra ändringar i nätverkskonfigurationsfilen i stället för att använda portalen. Om du gör ändringar i portalen skriver de över referensinställningarna för flera webbplatser för det här virtuella nätverket.

Du bör känna dig bekväm med att använda nätverkskonfigurationsfilen när du har slutfört proceduren för flera webbplatser. Men om du har flera personer som arbetar med nätverkskonfigurationen måste du se till att alla känner till den här begränsningen. Detta betyder inte att du inte kan använda portalen alls. Du kan använda den för allt annat, förutom att göra konfigurationsändringar i just det virtuella nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera, kontrollera att du har följande:

* Kompatibel VPN-maskinvara för varje lokal plats. Kontrollera [om VPN-enheter för virtuell nätverksanslutning för](vpn-gateway-about-vpn-devices.md) att kontrollera om den enhet som du vill använda är något som är känt för att vara kompatibel.
* En externt riktad offentlig IPv4 IP-adress för varje VPN-enhet. IP-adressen kan inte finnas bakom en NAT. Detta är krav.
* Någon som är duktig på att konfigurera din VPN-maskinvara. Du måste ha en stark förståelse för hur du konfigurerar din VPN-enhet eller arbetar med någon som gör det.
* DE IP-adressintervall som du vill använda för det virtuella nätverket (om du inte redan har skapat ett).
* IP-adressintervallen för var och en av de lokala nätverksplatser som du ansluter till. Du måste se till att IP-adressintervallen för var och en av de lokala nätverksplatser som du vill ansluta till inte överlappar varandra. Annars kommer portalen eller REST API avvisa konfigurationen som överförs.<br>Om du till exempel har två lokala nätverksplatser som båda innehåller IP-adressintervallet 10.2.3.0/24 och du har ett paket med måladressen 10.2.3.3, skulle Azure inte veta vilken plats du vill skicka paketet till eftersom adressintervallen överlappar varandra. För att förhindra routningsproblem tillåter Azure inte att du överför en konfigurationsfil som har överlappande områden.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Skapa en VPN-plats för plats
Om du redan har en Plats-till-plats VPN med en dynamisk routing gateway, bra! Du kan fortsätta att [exportera konfigurationsinställningarna för virtuellt nätverk](#export). Om inte, gör följande:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Om du redan har ett virtuellt nätverk för plats till plats, men det har en statisk (principbaserad) routningsgateway:
1. Ändra gatewaytypen till dynamisk routning. En VPN med flera webbplatser kräver en dynamisk (även känd som ruttbaserad) routningsgateway. Om du vill ändra gatewaytypen måste du först ta bort den befintliga gatewayen och sedan skapa en ny.
2. Konfigurera din nya gateway och skapa din VPN-tunnel. Instruktioner finns i Ange typen [SKU och VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Se till att du anger operationsföljdstypen som dynamisk.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Om du inte har ett virtuellt nätverk för plats till plats:
1. Skapa ditt virtuella nätverk för plats till plats med hjälp av följande instruktioner: [Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats](vpn-gateway-site-to-site-create.md).  
2. Konfigurera en dynamisk routningsgateway med hjälp av följande instruktioner: [Konfigurera en VPN Gateway](vpn-gateway-configure-vpn-gateway-mp.md). Var noga med att välja **dynamisk routning** för din gatewaytyp.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Exportera nätverkskonfigurationsfilen

Öppna PowerShell-konsolen med förhöjda rättigheter. Om du vill växla till tjänsthantering använder du det här kommandot:

```powershell
azure config mode asm
```

Anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Add-AzureAccount
```

Exportera konfigurationsfilen för Azure-nätverket genom att köra följande kommando. Du kan ändra platsen för filen så att den exporteras till en annan plats om det behövs.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Öppna nätverkskonfigurationsfilen
Öppna nätverkskonfigurationsfilen som du hämtade i det sista steget. Använd valfri xml-redigerare som du gillar. Filen ska se ut ungefär så här:

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

## <a name="4-add-multiple-site-references"></a>4. Lägg till flera platsreferenser
När du lägger till eller tar bort webbplatsreferensinformation ska du göra konfigurationsändringar i ConnectionsToLocalNetwork/LocalNetworkSiteRef. Om du lägger till en ny lokal platsreferens utlöses Azure för att skapa en ny tunnel. I exemplet nedan är nätverkskonfigurationen för en anslutning med en plats. Spara filen när du är klar med ändringarna.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Om du vill lägga till ytterligare platsreferenser (skapa en konfiguration med flera webbplatser) lägger du bara till ytterligare "LocalNetworkSiteRef"-rader, som visas i exemplet nedan:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importera nätverkskonfigurationsfilen
Importera nätverkskonfigurationsfilen. När du importerar filen med ändringarna läggs de nya tunnlarna till. Tunnlarna använder den dynamiska gateway som du skapade tidigare. Du kan använda PowerShell för att importera filen.

## <a name="6-download-keys"></a>6. Ladda ner nycklar
När dina nya tunnlar har lagts till använder du PowerShell-cmdleten Get-AzureVNetGatewayKey för att hämta IPsec/IKE-fördelade nycklar för varje tunnel.

Ett exempel:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Om du vill kan du också använda REST-API:et *för virtuell nätverksgateway* för att hämta de fördelade nycklarna.

## <a name="7-verify-your-connections"></a>7. Verifiera dina anslutningar
Kontrollera tunnelstatusen för flera webbplatser. När du har hämtat nycklarna för varje tunnel vill du verifiera anslutningar. Använd Get-AzureVnetConnection för att få en lista över virtuella nätverkstunnlar, som visas i exemplet nedan. VNet1 är namnet på det virtuella nätverket.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exempelretur:

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

Mer information om VPN-gateways finns i [Om VPN-gateways](vpn-gateway-about-vpngateways.md).
