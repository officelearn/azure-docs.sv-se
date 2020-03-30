---
title: Konfigurera Global VNet-peering för Azure Virtual WAN | Microsoft-dokument
description: Anslut ett virtuellt nätverk i en annan region till din Virtuella WAN-hubb.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588231"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Konfigurera Global VNet-peering (VNet över flera regioner) för virtuellt WAN

Du kan ansluta ett virtuellt nätverk i en annan region till din Virtuella WAN-hubb.

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande kriterier:

* Vnet (eker) mellan regioner är inte anslutet till ett annat virtuellt WAN-nav. En eker kan bara anslutas till en virtuell hubb.
* VNet (eker) innehåller inte en virtuell nätverksgateway (till exempel en Azure VPN Gateway eller ExpressRoute virtuell nätverksgateway). Om det virtuella nätverket innehåller en virtuell nätverksgateway måste du ta bort gatewayen innan du ansluter det eker-virtuella nätverket till navet.

## <a name="register-this-feature"></a><a name="register"></a>Registrera funktionen

Du kan registrera dig för den här funktionen med PowerShell. Om du väljer "Prova" i exemplet nedan öppnas Azure Cloud-Shell och du behöver inte installera PowerShell-cmdlets lokalt på din dator. Om det behövs kan du ändra prenumerationer med cmdleten <subid>Select-AzSubscription -SubscriptionId.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Verifiera registrering

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Ansluta ett virtuella nätverk till navet

I det här steget skapar du peering-anslutningen mellan hubben och det virtuella nätverket mellan regioner. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** för att skapa peering-anslutningen.

## <a name="next-steps"></a>Nästa steg

Mer information om Virtuellt WAN finns i [Virtual WAN Översikt](virtual-wan-about.md).