---
title: "Ta bort en virtuell nätverksgateway: PowerShell: klassiska Azure-portalen | Microsoft Docs"
description: "Ta bort en virtuell nätverksgateway med PowerShell i den klassiska distributionsmodellen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Ta bort en virtuell nätverksgateway med hjälp av PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassisk - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Den här artikeln hjälper dig att ta bort en VPN-gateway i den klassiska distributionsmodellen med hjälp av PowerShell. När den virtuella nätverksgatewayen har tagits bort, ändra konfigurationsfilen nätverk för att ta bort element som du inte längre använder.

##<a name="connect"></a>Steg 1: Ansluta till Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Installera de senaste PowerShell-cmdletarna.

Hämta och installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdlets. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Ansluta till ditt Azure-konto. 

Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Steg 2: Exportera och visa konfigurationsfilen nätverk

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. Du kan använda den här filen till båda visa den aktuella konfigurationsinformationen och även för att ändra nätverkskonfigurationen.

I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen i en textredigerare och visa namnet för din klassiska VNet. När du skapar ett VNet i Azure portal är det fullständiga namnet som använder Azure inte visas på portalen. Ett VNet som verkar vara med namnet 'ClassicVNet1' i Azure-portalen kan ha en mycket längre namn i konfigurationsfilen på nätverket. Namnet kan se ut ungefär så: 'Grupp ClassicRG1 ClassicVNet1'. Virtuella nätverksnamn listas som **' VirtualNetworkSite name ='**. Använda namnen i konfigurationsfilen på nätverket när du kör PowerShell-cmdlets.

## <a name="delete"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

När du tar bort en virtuell nätverksgateway kopplas alla anslutningar till virtuella nätverk via gatewayen. Om du har P2S-klienter som är anslutna till det virtuella nätverket, kopplas de utan varning.

Det här exemplet tar bort den virtuella nätverksgatewayen. Se till att använda det fullständiga namnet på det virtuella nätverket från konfigurationsfilen nätverk.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Om detta lyckas visar tillbaka:

```
Status : Successful
```

## <a name="modify"></a>Steg 4: Ändra konfigurationsfilen nätverk

När du tar bort en virtuell nätverksgateway ändrar cmdlet inte konfigurationsfilen nätverk. Du måste ändra den här filen för att ta bort de element som används inte längre. I följande avsnitt hjälpa dig att ändra konfigurationsfilen nätverk som du hämtat.

### <a name="lnsref"></a>Hänvisningar till lokalt nätverk

Om du vill ta bort platsen referensinformation, göra konfigurationsändringar i **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Tar bort en lokal plats referens utlösare Azure för att ta bort en tunnel. Beroende på konfigurationen som du har skapat, kanske du inte har en **LocalNetworkSiteRef** visas.

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

###<a name="lns"></a>Lokala nätverksplatser

Ta bort alla lokala webbplatser som du inte längre använder. Beroende på konfigurationen som du har skapat, är det möjligt att du inte har en **LocalNetworkSite** visas.

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

I det här exemplet bort vi bara Site3.

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

### <a name="clientaddresss"></a>Klientadresspool

Om du har en P2S-anslutning till ditt VNet, har du en **VPNClientAddressPool**. Ta bort de klient-adresspooler som motsvarar den virtuella nätverksgatewayen som tagits bort.

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

Ta bort den **GatewaySubnet** som motsvarar VNet.

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

## <a name="upload"></a>Steg 5: Överför konfigurationsfilen nätverk

Spara dina ändringar och överför konfigurationsfilen nätverk till Azure. Kontrollera att du ändrar sökvägen till filen som behövs för din miljö.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Om detta lyckas visar tillbaka något liknar det här exemplet:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded