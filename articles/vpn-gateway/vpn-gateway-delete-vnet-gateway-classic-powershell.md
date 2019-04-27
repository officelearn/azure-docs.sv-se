---
title: 'Ta bort en virtuell nätverksgateway: PowerShell: Azure klassiska | Microsoft Docs'
description: Ta bort en virtuell nätverksgateway med PowerShell i den klassiska distributionsmodellen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863988"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Ta bort en virtuell nätverksgateway med hjälp av PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassisk – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Den här artikeln får du ta bort en VPN-gateway i den klassiska distributionsmodellen med hjälp av PowerShell. När den virtuella nätverksgatewayen har tagits bort, ändra nätverkskonfigurationsfilen för att ta bort element som du inte längre använder.

## <a name="connect"></a>Steg 1: Anslut till Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installera det senaste PowerShell-cmdlet.

Hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto. 

Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Steg 2: Exportera och visa nätverkskonfigurationsfilen

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. Du kan använda den här filen till båda visa den aktuella konfigurationsinformationen och även för att ändra nätverkskonfigurationen.

I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en textredigerare och visa namnet på ditt klassiska virtuella nätverk. När du skapar ett virtuellt nätverk i Azure-portalen, visas inte det fullständiga namnet som använder Azure i portalen. Ett virtuellt nätverk som verkar ha namnet ”ClassicVNet1” i Azure-portalen kan till exempel ha en mycket längre namn i nätverkskonfigurationsfilen. Namnet kan se ut ungefär som: Gruppera ClassicRG1 ClassicVNet1. Virtuella nätverksnamn listas som **' VirtualNetworkSite name ='**. Använda namnen i nätverkskonfigurationsfilen när du kör PowerShell-cmdletar.

## <a name="delete"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

När du tar bort en virtuell nätverksgateway kan kopplas alla anslutningar till det virtuella nätverket via gatewayen. Om du har P2S-klienter som är anslutna till det virtuella nätverket kan de kommer att kopplas från utan varning.

Det här exemplet tar bort den virtuella nätverksgatewayen. Se till att använda det fullständiga namnet på det virtuella nätverket från nätverkskonfigurationsfilen.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Om detta lyckas visar avkastningen:

```
Status : Successful
```

## <a name="modify"></a>Steg 4: Ändra nätverkskonfigurationsfilen

När du tar bort en virtuell nätverksgateway ändrar cmdleten inte nätverkskonfigurationsfilen. Du måste ändra filen för att ta bort de element som används inte längre. I följande avsnitt hjälpa dig ändra nätverkskonfigurationsfilen som du laddade ned.

### <a name="lnsref"></a>Hänvisningar till lokalt nätverk

Om du vill ta bort referensinformation för platsen, göra konfigurationsändringar i **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Ta bort en lokal plats på en referens-utlösare Azure för att ta bort en tunnel. Beroende på konfigurationen som du skapade, kanske du inte har en **LocalNetworkSiteRef** visas.

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

### <a name="lns"></a>Lokala nätverksplatser

Ta bort lokala webbplatser som du inte längre använder. Beroende på hur du har skapat, är det möjligt att du inte har en **LocalNetworkSite** visas.

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

I det här exemplet vi har tagit bort bara Site3.

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

### <a name="clientaddresss"></a>Client AddressPool

Om du har en P2S-anslutning till ditt VNet, får du en **VPNClientAddressPool**. Ta bort de klient-adresspooler som motsvarar den virtuella nätverksgatewayen som du har tagit bort.

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

### <a name="gwsub"></a>GatewaySubnet

Ta bort den **GatewaySubnet** som motsvarar det virtuella nätverket.

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

## <a name="upload"></a>Steg 5: Ladda upp nätverkskonfigurationsfilen

Spara dina ändringar och ladda upp nätverkskonfigurationsfilen till Azure. Kontrollera att du ändrar filsökväg som krävs för miljön.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Om detta lyckas visar avkastningen liknande den i följande exempel:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
