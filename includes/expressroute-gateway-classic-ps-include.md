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
ms.openlocfilehash: 2457ef2843b0d16359b7e47fc54c58e2ef5e6034
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430046"
---
> [!NOTE]
> De här exemplen gäller inte för S2S/ExpressRoute samexistera konfigurationer.
> Läs mer om hur du arbetar med gatewayer i en konfiguration med coexist [konfigurerar du samexisterande anslutningar.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Lägga till en gateway

När du lägger till en gateway till ett virtuellt nätverk med den klassiska modellen måste ändra du nätverkskonfigurationsfilen direkt innan du skapar gatewayen. Värden i exemplen nedan måste finnas i filen för att skapa en gateway. Om det virtuella nätverket tidigare hade en gateway som är associerade med den, vara några av dessa värden redan ifylld. Ändra filen för att återspegla värdena nedan.

### <a name="download-the-network-configuration-file"></a>Ladda ned nätverkskonfigurationsfilen

1. Ladda ned nätverkskonfigurationsfilen med hjälp av stegen i [nätverkskonfigurationsfilen](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) artikeln. Öppna filen i en textredigerare.
2. Lägg till en lokal nätverksplats i filen. Du kan använda valfri giltig adressprefix. Du kan lägga till någon giltig IP-adress för VPN-gateway. Adressvärden i det här avsnittet används inte för ExpressRoute-åtgärder, men krävs för verifiering av filen. I det här exemplet är ”branch1” namnet på platsen. Du kan använda ett annat namn, men se till att använda samma värde i avsnittet Gateway filens.

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

  * Kontrollera att Gateway-undernätet finns för det virtuella nätverket. Om det inte kan du lägga till något just nu. Namnet måste vara ”GatewaySubnet”.
  * Kontrollera avsnittet Gateway filens finns. Om inte kan du lägga till den. Detta krävs för att koppla det virtuella nätverket till den lokala nätverksplatsen (som representerar det nätverket som du ansluter).
  * Kontrollera att anslutningstypen = dedikerad. Detta krävs för ExpressRoute-anslutningar.

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
4. Spara filen och överföra den till Azure.

### <a name="create-the-gateway"></a>Skapa gatewayen

Använd kommandot nedan för att skapa en gateway. Ersätt alla värden för dina egna.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Kontrollera att gatewayen har skapats

Använd kommandot nedan för att verifiera att gatewayen har skapats. Detta kommando hämtar också gateway-ID som du behöver för andra åtgärder.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Det finns ett antal [Gateway SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra den Gateway-SKU när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-gateway. Om du vill ändra din gateway till en UltraPerformance-gateway, först ta bort den befintliga ExpressRoute-gatewayen och skapa sedan en ny UltraPerformance-gateway. Ta först bort UltraPerformance-gateway för att nedgradera din gateway från en UltraPerformance-gateway och sedan skapa en ny gateway.
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