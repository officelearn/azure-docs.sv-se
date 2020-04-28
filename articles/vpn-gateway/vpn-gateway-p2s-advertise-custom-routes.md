---
title: 'Azure VPN Gateway: annonsera anpassade vägar för P2S VPN-klienter'
description: Steg för att annonsera anpassade vägar till dina punkt-till-plats-klienter
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756896"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Annonsera anpassade vägar för P2S VPN-klienter

Du kanske vill annonsera anpassade vägar till alla VPN-klienter från punkt till plats. Till exempel när du har aktiverat lagrings slut punkter i ditt VNet och vill att fjärran vändare ska kunna komma åt dessa lagrings konton via VPN-anslutningen. Du kan annonsera IP-adressen för lagrings slut punkten till alla fjärran vändare så att trafiken till lagrings kontot går via VPN-tunneln och inte på det offentliga Internet.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Annonsera anpassade vägar

Om du vill annonsera anpassade vägar använder `Set-AzVirtualNetworkGateway cmdlet`du. I följande exempel visas hur du annonserar IP-adressen för [contoso Storage Account-tabeller](https://contoso.table.core.windows.net).

1. Pinga *contoso.Table.Core.Windows.net* och anteckna IP-adressen. Ett exempel:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Kör följande PowerShell-kommandon:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Om du vill lägga till flera anpassade vägar använder du kommatecken och blank stegen för att avgränsa adresserna. Ett exempel:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Visa anpassade vägar

Använd följande exempel för att visa anpassade vägar:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Ta bort anpassade vägar

Använd följande exempel för att ta bort anpassade vägar:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Nästa steg

Mer information om P2S-routning finns i [om punkt-till-plats-routning](vpn-gateway-about-point-to-site-routing.md).
