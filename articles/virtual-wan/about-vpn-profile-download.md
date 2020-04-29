---
title: 'Azure Virtual WAN: användar profiler för VPN-klienter'
description: Detta hjälper dig att arbeta med klient profil filen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066211"
---
# <a name="working-with-user-vpn-client-profiles"></a>Arbeta med användar-VPN-klient profiler

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en användares VPN-klientkonfiguration.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nästa steg

Mer information om VPN för virtuella WAN-användare finns i [skapa en VPN-anslutning för användare](virtual-wan-point-to-site-portal.md).
