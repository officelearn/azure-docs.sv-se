---
title: 'Azure VPN Gateway: Annonsera anpassade vägar för P2S VPN-klienter'
description: Steg för att annonsera anpassade vägar till dina point-to-site-klienter
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3588755e2aab1c84d443e917eca8c7fca280b49a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756896"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Annonsera anpassade rutter för P2S VPN-klienter

Du kanske vill annonsera anpassade vägar till alla dina point-to-site VPN-klienter. Till exempel när du har aktiverat lagringsslutpunkter i ditt virtuella nätverk och vill att fjärranvändarna ska kunna komma åt dessa lagringskonton via VPN-anslutningen. Du kan annonsera IP-adressen för lagringsslutpunkten till alla fjärranvändare så att trafiken till lagringskontot går över VPN-tunneln och inte till det offentliga Internet.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Så här annonserar du anpassade rutter

Om du vill annonsera `Set-AzVirtualNetworkGateway cmdlet`anpassade vägar använder du . I följande exempel visas hur du annonserar IP för [Contoso-lagringskontotabellerna](https://contoso.table.core.windows.net).

1. Pinga *contoso.table.core.windows.net* och notera IP-adressen. Ett exempel:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Kör följande PowerShell-kommandon:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Om du vill lägga till flera anpassade vägar använder du ett kommatecken och blanksteg för att avgränsa adresserna. Ett exempel:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Så här visar du anpassade vägar

Använd följande exempel för att visa anpassade vägar:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Så här tar du bort anpassade vägar

Använd följande exempel för att ta bort anpassade vägar:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Nästa steg

Mer P2S-routningsinformation finns i [Om routning från punkt till plats](vpn-gateway-about-point-to-site-routing.md).
