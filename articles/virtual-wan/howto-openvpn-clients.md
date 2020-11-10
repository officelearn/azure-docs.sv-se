---
title: Konfigurera OpenVPN-klienter för Azure Virtual WAN
description: Steg för att konfigurera OpenVPN-klienter för Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3e9674916ea9bb5e756a5e57ff18517f53ca7497
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427566"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurera en OpenVPN-klient för Azure Virtual WAN

Den här artikeln hjälper dig att konfigurera **OpenVPN- &reg; protokoll** klienter. Du kan också använda Azure VPN-klienten för Windows 10 för att ansluta via OpenVPN-protokollet 

## <a name="before-you-begin"></a>Innan du börjar

Skapa en användare VPN-konfiguration (punkt-till-plats). Kontrol lera att du väljer "OpenVPN" som tunnel typ. Anvisningar finns i [skapa en P2s-konfiguration för Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om användar-VPN (punkt-till-plats) finns i [skapa användare VPN-anslutningar](virtual-wan-point-to-site-portal.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
