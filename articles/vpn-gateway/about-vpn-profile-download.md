---
title: 'Azure VPN Gateway: om P2S VPN-klientinställningar'
description: Detta hjälper dig att arbeta med klient profil filen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528514"
---
# <a name="about-p2s-vpn-client-profiles"></a>Om P2S VPN-klientinställningar

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klient profil.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter för Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows). Den här mappen kommer inte att finnas i zip-filen om Azure AD-autentisering har valts på VPN-gatewayen. I stället kommer azurevpnconfig. XML att finnas i mappen AzureVPN.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [om punkt-till-plats](point-to-site-about.md).
