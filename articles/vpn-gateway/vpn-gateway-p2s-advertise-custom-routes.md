---
title: Annonsera anpassade vägar för punkt-till-plats-VPN-klienter | Azure | Microsoft Docs
description: Steg för att annonsera anpassade vägar till dina punkt-till-plats-klienter
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2019
ms.author: cherylmc
ms.openlocfilehash: 18a0effcf5157ec2797707db78f9614ef12a4669
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310248"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Annonsera anpassade vägar för P2S VPN-klienter

Du kanske vill annonsera anpassade vägar till alla VPN-klienter från punkt till plats. Till exempel när du har aktiverat lagrings slut punkter i ditt VNet och vill att fjärran vändare ska kunna komma åt dessa lagrings konton via VPN-anslutningen. Du kan annonsera IP-adressen för lagrings slut punkten till alla fjärran vändare så att trafiken till lagrings kontot går via VPN-tunneln och inte på det offentliga Internet.

## <a name="to-advertise-custom-routes"></a>Annonsera anpassade vägar

Om du vill annonsera anpassade vägar använder `Set-AzVirtualNetworkGateway cmdlet`du. I följande exempel visas hur du annonserar IP-adressen för [lagrings konto tabellerna i Conotoso](https://contoso.table.core.windows.net).

1. Pinga *contoso.Table.Core.Windows.net* och anteckna IP-adressen. Exempel:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Kör följande PowerShell-kommandon:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Om du vill lägga till flera anpassade vägar använder du ett Coma och blank steg för att avgränsa adresserna. Exempel:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Visa anpassade vägar

Använd följande exempel för att visa anpassade vägar:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    $gw.CustomRoutes | Format-List
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om P2S-routning finns i [om punkt-till-plats-routning](vpn-gateway-about-point-to-site-routing.md).
