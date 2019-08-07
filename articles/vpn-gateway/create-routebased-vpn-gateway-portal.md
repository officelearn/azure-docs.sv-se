---
title: 'Skapa en Route-baserad VPN-Gateway: Azure Portal | Microsoft Docs'
description: Skapa en Route-baserad VPN Gateway med hjälp av Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781177"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Skapa en Route-baserad VPN-gateway med hjälp av Azure Portal

Den här artikeln hjälper dig att snabbt skapa en Route-baserad Azure VPN-gateway med hjälp av Azure Portal.  En VPN-gateway används när du skapar en VPN-anslutning till ditt lokala nätverk. Du kan också använda en VPN-gateway för att ansluta virtuella nätverk. 

Stegen i den här artikeln skapar ett VNet, ett undernät, ett Gateway-undernät och en Route-baserad VPN-gateway (virtuell nätverksgateway). När gatewayen har skapats kan du skapa anslutningar. De här stegen kräver en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="vnet"></a>Skapa ett virtuellt nätverk

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>Lägg till ett Gateway-undernät

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>Konfigurera och skapa gatewayen

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Basic Gateway-SKU: n stöder inte IKEv2-eller RADIUS-autentisering. Om du planerar att låta Mac-klienter ansluta till ditt virtuella nätverk ska du inte använda den grundläggande SKU: n.

## <a name="viewgw"></a>Visa VPN-gatewayen

1. När gatewayen har skapats navigerar du till VNet1 i portalen. VPN-gatewayen visas på sidan Översikt som en ansluten enhet.

   ![Anslutna enheter](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Anslutna enheter")

2. I enhets listan klickar du på **VNet1GW** för att visa mer information.

   ![Visa VPN-gateway](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Visa VPN-gateway")

## <a name="next-steps"></a>Nästa steg

När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat VNet. Eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats.

> [!div class="nextstepaction"]
> [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Skapa en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Skapa en anslutning till ett annat VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
