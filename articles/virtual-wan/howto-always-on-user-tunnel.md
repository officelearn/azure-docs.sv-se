---
title: Konfigurera en tunnel för alltid-on VPN-användare
titleSuffix: Azure Virtual WAN
description: Den här artikeln beskriver hur du konfigurerar en virtuell WAN-användar tunnel för ditt virtuella WAN-nätverk
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/22/2020
ms.author: cherylmc
ms.openlocfilehash: 03f67053a5a199c8c64efb05d2b6a65ad6707650
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564057"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Konfigurera en användar tunnel för alltid på VPN för virtuellt WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Krav

Du måste skapa en punkt-till-plats-konfiguration och redigera den virtuella hubben. Anvisningar finns i följande avsnitt:

* [Skapa en P2S-konfiguration](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Skapa hubb med P2S-Gateway](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Konfigurera en användar tunnel

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Använd följande steg för att ta bort en profil:

1. Kör följande kommando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppla från anslutningen och avmarkera kryss rutan **Anslut automatiskt** .

   ![Rensa](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
