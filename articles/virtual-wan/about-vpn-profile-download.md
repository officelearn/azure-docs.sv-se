---
title: 'Azure Virtual WAN: användar profiler för VPN-klienter'
description: Detta hjälper dig att arbeta med klient profil filen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 116ec518f7788e620fc6bd177e535c8f6af99d10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267779"
---
# <a name="working-with-user-vpn-client-profiles"></a>Arbeta med användar-VPN-klient profiler

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en användares VPN-klientkonfiguration.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nästa steg

Mer information om VPN för virtuella WAN-användare finns i [skapa en VPN-anslutning för användare](virtual-wan-point-to-site-portal.md).
