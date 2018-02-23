---
title: "Ansluta ett virtuellt nätverk till flera platser med hjälp av VPN-Gateway och PowerShell: klassiska | Microsoft Docs"
description: "Ansluta flera lokala lokala platser till ett klassiskt virtuellt nätverk med en VPN-Gateway."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: c0af4271df0e88354edb717b8d6f4c99ab29e573
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN-gateway-anslutning (klassiskt)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
>

Den här artikeln vägleder dig genom att använda PowerShell för att lägga till plats-till-plats (S2S)-anslutningar till en VPN-gateway som har en befintlig anslutning. Den här typen av anslutning kallas ofta ”flera” platskonfiguration. Stegen i den här artikeln gäller för virtuella nätverk som skapats med den klassiska distributionsmodellen (även kallat servicehantering). Dessa steg gäller inte för konfigurationer för samtidiga ExpressRoute/plats-till-plats-anslutning.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdatera den här tabellen som nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När det finns en artikel länka det till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Om hur du ansluter

Du kan ansluta flera lokala platser till ett enda virtuellt nätverk. Detta är särskilt tilltalande för att skapa hybrid molnlösningar. Skapa en anslutning för flera platser till din Azure virtuella nätverksgateway liknar skapa andra plats-till-plats-anslutningar. Faktum är du kan använda en befintlig Azure VPN-gateway som en gateway är dynamisk (route-baserade).

Om du redan har en statisk gateway anslutna till det virtuella nätverket kan du ändra gateway-typen till dynamisk utan att behöva återskapa det virtuella nätverket för att kunna hantera flera platser. Innan du ändrar typen routning, kontrollerar du att din lokala VPN-gateway stöder ruttbaserad VPN-konfigurationer.

![diagram över flera platser](./media/vpn-gateway-multi-site/multisite.png "flera platser")

## <a name="points-to-consider"></a>Saker att tänka på

**Du kan inte använda portalen för att göra ändringar i det här virtuella nätverket.** Du måste göra ändringar i konfigurationsfilen för nätverk i stället för med hjälp av portalen. Om du gör ändringar i portalen får de över flera platser referens inställningarna för det här virtuella nätverket.

Du bör känna sig fria att använda konfigurationsfilen nätverk när du har slutfört proceduren för flera platser. Om du har flera personer arbetar på din nätverkskonfiguration måste du dock se till att alla vet om den här begränsningen. Det betyder att du inte kan använda portalen alls. Du kan använda det för allt annat, förutom att göra konfigurationsändringar till viss virtuella nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar konfigurera måste du kontrollera att du har följande:

* Kompatibel VPN maskinvara för var och en lokal plats. Kontrollera [om VPN-enheter för virtuell nätverksanslutning](vpn-gateway-about-vpn-devices.md) att kontrollera om enheten som du vill använda är något som kallas för att vara kompatibel.
* Ett externt Internetriktade offentliga IPv4-adress för varje VPN-enhet. IP-adressen kan inte finnas bakom en NAT Detta är ett krav.
* Du måste installera den senaste versionen av Azure PowerShell-cmdletarna. Kontrollera att du installerar Service Management (SM) version förutom Resource Manager-version. Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för mer information.
* Någon som skicklig på Konfigurera VPN-maskinvara. Du måste ha en stark förståelse för hur du konfigurerar VPN-enhet eller arbeta med någon som.
* IP-adressintervall som du vill använda för ditt virtuella nätverk (om du inte redan har skapat en).
* IP-adressintervall för var och en av de lokala nätverksplatser som du ska kunna ansluta till. Du måste se till att adressintervall för var och en av de lokala nätverksplatser som du vill ansluta till inte överlappar varandra. Annars avvisar portalen eller REST API konfigurationen som överförs.<br>Om du har två lokala nätverksplatser som innehåller båda IP-adressintervallet 10.2.3.0/24 och du har ett paket med en måladress 10.2.3.3 skulle Azure vet vilken plats som du vill skicka paketet till eftersom adressintervallen överlappar. För att förhindra problem med Routning Azure tillåter inte att du överför en fil som har överlappande intervall.

## <a name="1-create-a-site-to-site-vpn"></a>1. Skapa en VPN för plats-till-plats
Om du redan har en plats-till-plats VPN-anslutning med en dynamisk routning gateway utmärkt! Du kan fortsätta att [exportera konfigurationsinställningar virtuella nätverk](#export). Om inte, gör du följande:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Om du redan har ett virtuellt nätverk för plats-till-plats, men den har en statisk routning (principbaserad)-gateway.
1. Ändra din gateway till dynamisk routning. En VPN-anslutning för flera platser kräver en dynamisk routning (även kallat ruttbaserad)-gateway. Om du vill ändra din gatewaytyp, måste du först ta bort den befintliga gatewayen och sedan skapa en ny.
2. Konfigurera din nya gateway och skapa VPN-tunnel. Instruktioner anvisningar finns i [anger SKU- och VPN-](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Kontrollera att du anger typen av routning som ”Dynamic”.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Om du inte har ett virtuellt nätverk för plats-till-plats:
1. Skapa det virtuella nätverket på plats-till-plats använder dessa instruktioner: [skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning](vpn-gateway-site-to-site-create.md).  
2. Konfigurera en dynamisk routning gateway som använder dessa instruktioner: [konfigurera en VPN-Gateway](vpn-gateway-configure-vpn-gateway-mp.md). Se till att välja **dynamisk routning** för gateway-typen.

## <a name="export"></a>2. Exportera konfigurationsfilen nätverk
Exportera konfigurationsfilen Azure-nätverk genom att köra följande kommando. Du kan ändra platsen för den fil som ska exporteras till en annan plats om det behövs.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Öppna konfigurationsfilen nätverk
Öppna konfigurationsfilen för nätverket som du hämtade i det sista steget. Använda en xml-redigerare som du vill använda. Filen bör se ut ungefär så här:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="4-add-multiple-site-references"></a>4. Lägga till flera hänvisningar
När du lägger till eller ta bort platsen Referensinformation ska du göra konfigurationsändringar till ConnectionsToLocalNetwork/LocalNetworkSiteRef. Lägger till en ny lokal plats referens utlösare Azure för att skapa en ny tunnel. I exemplet nedan är nätverkskonfigurationen för en enskild plats-anslutning. Spara filen när du är klar med ändringarna.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Lägg till ytterligare referenser (skapa en konfiguration för flera platser), bara lägga till ytterligare ”LocalNetworkSiteRef” rader som visas i exemplet nedan:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importera konfigurationsfilen nätverk
Importera konfigurationsfilen nätverk. När du importerar filen med ändringarna läggs de nya tunnlarna. Tunnlar använder den dynamiska gateway som du skapade tidigare. Du kan använda PowerShell för att importera filen.

## <a name="6-download-keys"></a>6. Hämta nycklar
När din nya tunnlar har lagts till, kan du använda PowerShell-cmdleten 'Get-AzureVNetGatewayKey' för att få IPsec/IKE i förväg delade nycklar för varje tunnel.

Exempel:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Om du vill kan du också använda den *hämta virtuella nätverk Gateway delad nyckel* REST API för att hämta de i förväg delade nycklarna.

## <a name="7-verify-your-connections"></a>7. Verifiera dina anslutningar
Kontrollera statusen för flera platser tunnel. När du har hämtat nycklarna för varje tunnel vill du verifiera anslutningar. Använd 'Get-AzureVnetConnection' för att hämta en lista över virtuella nätverk tunnlar, som visas i exemplet nedan. VNet1 är namnet på VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exempel returnerar:

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

Mer information om VPN-gatewayer finns [om VPN-gatewayer](vpn-gateway-about-vpngateways.md).
