---
title: Konfigurera en VPN-tunnel som alltid är på
titleSuffix: Azure Virtual WAN
description: Steg för att konfigurera Always on VPN-enhets tunnel för virtuellt WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e911bf6e3736c931ca5c1563ab42f52ecb5cf3c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750573"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurera en tunnel för alltid på VPN-enheter för virtuellt WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Krav

Du måste skapa en punkt-till-plats-konfiguration och redigera den virtuella hubben. Anvisningar finns i följande avsnitt:

* [Skapa en P2S-konfiguration](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Redigera Hub-tilldelningen](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurera enhets tunneln

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Om du vill ta bort profilen kör du följande kommando:

![Rensa](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).