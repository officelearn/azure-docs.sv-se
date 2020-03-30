---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187077"
---
> [!NOTE]
> Dessa exempel gäller inte för S2S/ExpressRoute samexistera konfigurationer.
> Mer information om hur du arbetar med gateways i en samexistera konfiguration finns i [Konfigurera samtidiga anslutningar.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Lägga till en gateway

När du lägger till en gateway i ett virtuellt nätverk med den klassiska resursmodellen ändrar du nätverkskonfigurationsfilen direkt innan gatewayen skapas. Värdena i exemplen nedan måste finnas i filen för att skapa en gateway. Om det virtuella nätverket tidigare hade en gateway kopplad till sig finns redan några av dessa värden. Ändra filen så att den återspeglar värdena nedan.

### <a name="download-the-network-configuration-file"></a>Hämta nätverkskonfigurationsfilen

1. Hämta nätverkskonfigurationsfilen med hjälp av stegen i artikeln [om nätverkskonfiguration.](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) Öppna filen med hjälp av en textredigerare.
2. Lägg till en lokal nätverksplats i filen. Du kan använda valfritt giltigt adressprefix. Du kan lägga till en giltig IP-adress för VPN-gatewayen. Adressvärdena i det här avsnittet används inte för ExpressRoute-åtgärder, men krävs för filverifiering. I exemplet är "branch1" namnet på webbplatsen. Du kan använda ett annat namn, men se till att använda samma värde i avsnittet Gateway i filen.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navigera till VirtualNetworkSites och ändra fälten.

   * Kontrollera att gateway-undernätet finns för det virtuella nätverket. Om den inte gör det kan du lägga till en just nu. Namnet måste vara "GatewaySubnet".
   * Kontrollera att avsnittet Gateway i filen finns. Om den inte gör det, lägg till den. Detta krävs för att associera det virtuella nätverket med den lokala nätverksplatsen (som representerar det nätverk som du ansluter till).
   * Kontrollera att anslutningstypen = Dedikerad. Detta krävs för ExpressRoute-anslutningar.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Spara filen och ladda upp den till Azure.

### <a name="create-the-gateway"></a>Skapa gatewayen

Använd kommandot nedan för att skapa en gateway. Ersätt alla värden mot dina egna.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verifiera att gatewayen har skapats

Använd kommandot nedan för att kontrollera att gatewayen har skapats. Det här kommandot hämtar också gateway-ID: et, som du behöver för andra åtgärder.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Det finns ett antal [SKU:er för gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan när som helst använda följande kommando för att ändra gateway-SKU: n.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance gateway. Om du vill ändra din gateway till en UltraPerformance-gateway tar du först bort den befintliga ExpressRoute-gatewayen och skapar sedan en ny UltraPerformance-gateway. Om du vill nedgradera gatewayen från en UltraPerformance-gateway tar du först bort UltraPerformance-gatewayen och skapar sedan en ny gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Ta bort en gateway

Använd kommandot nedan för att ta bort en gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```