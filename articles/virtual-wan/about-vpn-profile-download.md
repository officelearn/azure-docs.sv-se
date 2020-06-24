---
title: 'Azure Virtual WAN: användar profiler för VPN-klienter'
description: Detta hjälper dig att arbeta med klient profil filen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753147"
---
# <a name="working-with-user-vpn-client-profiles"></a>Arbeta med användar-VPN-klient profiler

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en användares VPN-klientkonfiguration.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nästa steg

Mer information om VPN för virtuella WAN-användare finns i [skapa en VPN-anslutning för användare](virtual-wan-point-to-site-portal.md).
