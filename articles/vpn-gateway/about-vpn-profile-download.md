---
title: 'Azure VPN Gateway: om P2S VPN-klientinställningar'
description: Använd den här artikeln för att hitta den information du behöver för en VPN-klient profil.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985928"
---
# <a name="about-p2s-vpn-client-profiles"></a>Om P2S VPN-klientinställningar

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klient profil.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter för Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows). Om Azure AD-autentisering har valts på VPN-gatewayen finns inte den här mappen i zip-filen. Navigera i stället till mappen AzureVPN och leta upp azurevpnconfig.xml.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [om punkt-till-plats](point-to-site-about.md).
