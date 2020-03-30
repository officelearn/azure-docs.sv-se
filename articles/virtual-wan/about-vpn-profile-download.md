---
title: 'Azure Virtual WAN: Vpn-klientprofiler för användare'
description: Detta hjälper dig att arbeta med klientprofilfilen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: b88be5cccdaeb452288a2cb2f1dd4b4690a8b274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066211"
---
# <a name="working-with-user-vpn-client-profiles"></a>Arbeta med klientprofiler för Användare VPN

Den nedladdade profilfilen innehåller information som är nödvändig för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en användar-VPN-klientprofil.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN-mappen** innehåller *ovpn-profilen* som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [Konfigurera OpenVPN-klienter](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nästa steg

Mer information om Vpn för Virtual WAN-användare finns i [Skapa en VPN-anslutning för användare](virtual-wan-point-to-site-portal.md).
