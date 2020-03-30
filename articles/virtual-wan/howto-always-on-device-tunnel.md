---
title: Konfigurera en VPN-tunnel som alltid är på
titleSuffix: Azure Virtual WAN
description: Steg för att konfigurera Alltid på VPN-enhetstunnel för VirtuellT WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502859"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurera en alltid på VPN-enhetstunnel för Virtuellt WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Krav

Du måste skapa en point-to-site-konfiguration och redigera den virtuella hubbtilldelningen. Se följande avsnitt för instruktioner:

* [Skapa en P2S-konfiguration](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Redigera hubbtilldelningen](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurera enhetstunneln

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Så här tar du bort en profil

Om du vill ta bort profilen kör du följande kommando:

![Rensa](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Virtuellt WAN finns i [vanliga frågor](virtual-wan-faq.md)och svar .