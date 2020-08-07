---
title: Konfigurera en VPN-tunnel som alltid är på
titleSuffix: Azure VPN Gateway
description: Lär dig hur du konfigurerar Always on, som upprätthåller en VPN-anslutning baserat på utlösare, till exempel användar inloggning, ändring av nätverks tillstånd eller enhets skärmen aktiv.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927070"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurera en VPN-enhetstunnel för AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

Konfigurera VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering med hjälp av artikeln [Konfigurera en punkt-till-plats-VPN-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Konfigurera enhets tunneln

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Om du vill ta bort profilen kör du följande kommando:

![Rensa](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Information om fel sökning finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
