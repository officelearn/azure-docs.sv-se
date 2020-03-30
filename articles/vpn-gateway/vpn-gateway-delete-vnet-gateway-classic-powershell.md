---
title: 'Ta bort en virtuell nätverksgateway: Azure classic'
description: Ta bort en virtuell nätverksgateway med PowerShell i den klassiska distributionsmodellen.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198657"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Ta bort en virtuell nätverksgateway med PowerShell (klassiskt)

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassiskt – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Den här artikeln hjälper dig att ta bort en VPN-gateway i den klassiska distributionsmodellen med hjälp av PowerShell. När den virtuella nätverksgatewayen har tagits bort ändrar du nätverkskonfigurationsfilen för att ta bort element som du inte längre använder.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Steg 1: Ansluta till Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installera de senaste PowerShell-cmdlets.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto.

Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

1. Öppna PowerShell-konsolen med förhöjda rättigheter. Om du vill växla till tjänsthantering använder du det här kommandot:

   ```powershell
   azure config mode asm
   ```
2. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Steg 2: Exportera och visa nätverkskonfigurationsfilen

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. Du använder den här filen för att både visa aktuell konfigurationsinformation och även för att ändra nätverkskonfigurationen.

I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en textredigerare och visa namnet på ditt klassiska virtuella nätverk. När du skapar ett virtuella nätverk i Azure-portalen visas inte det fullständiga namnet som Azure använder i portalen. Ett virtuella nätverk som verkar heta "ClassicVNet1" i Azure-portalen kan till exempel ha ett mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: "Group ClassicRG1 ClassicVNet1". Virtuella nätverksnamn listas som **"VirtualNetworkSite name ='**. Använd namnen i nätverkskonfigurationsfilen när du kör dina PowerShell-cmdlets.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

När du tar bort en virtuell nätverksgateway kopplas alla anslutningar till det virtuella nätverket via gatewayen från. Om du har P2S-klienter anslutna till det virtuella nätverket kopplas de bort utan förvarning.

I det här exemplet tas den virtuella nätverksgatewayen bort. Se till att använda det fullständiga namnet på det virtuella nätverket från nätverkskonfigurationsfilen.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Om det lyckas visar returen:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Steg 4: Ändra nätverkskonfigurationsfilen

När du tar bort en virtuell nätverksgateway ändrar cmdleten inte nätverkskonfigurationsfilen. Du måste ändra filen för att ta bort de element som inte längre används. Följande avsnitt hjälper dig att ändra nätverkskonfigurationsfilen som du hämtade.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Referenser till lokala nätverkswebbplatser

Om du vill ta bort platsreferensinformation gör du **konfigurationsändringar i ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Om du tar bort en lokal platsreferens utlöses Azure för att ta bort en tunnel. Beroende på vilken konfiguration du har skapat kanske du inte har en **LocalNetworkSiteRef** i listan.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Exempel:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Lokala nätverkswebbplatser

Ta bort alla lokala webbplatser som du inte längre använder. Beroende på vilken konfiguration du har skapat är det möjligt att du inte har en **LocalNetworkSite** i listan.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

I det här exemplet har vi bara tagit bort Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Klientadresspool

Om du hade en P2S-anslutning till ditt virtuella nätverk har du en **VPNClientAddressPool**. Ta bort klientadresspoolerna som motsvarar den virtuella nätverksgateway som du har tagit bort.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Exempel:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Ta bort **GatewaySubnet** som motsvarar det virtuella nätverket.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Exempel:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Steg 5: Ladda upp nätverkskonfigurationsfilen

Spara ändringarna och ladda upp nätverkskonfigurationsfilen till Azure. Se till att du ändrar sökvägen efter behov för din miljö.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Om det lyckas visar returen något som liknar det här exemplet:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
