---
title: 'Azure VPN Gateway: om P2S VPN-klientinställningar'
description: Detta hjälper dig att arbeta med klient profil filen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83650650"
---
# <a name="about-p2s-vpn-client-profiles"></a>Om P2S VPN-klientinställningar

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klient profil.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter för Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows). Om Azure AD-autentisering har valts på VPN-gatewayen finns inte den här mappen i zip-filen. Navigera i stället till mappen AzureVPN och leta upp azurevpnconfig.xml.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [om punkt-till-plats](point-to-site-about.md).
