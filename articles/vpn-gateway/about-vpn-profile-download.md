---
title: 'Azure VPN Gateway: Om P2S VPN-klientprofiler'
description: Detta hjälper dig att arbeta med klientprofilfilen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528514"
---
# <a name="about-p2s-vpn-client-profiles"></a>Om P2S VPN-klientprofiler

Den nedladdade profilfilen innehåller information som är nödvändig för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klientprofil.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **OpenVPN-mappen** innehåller *ovpn-profilen* som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [Konfigurera OpenVPN-klienter för Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Den här mappen kommer inte att finnas i zip-filen om Azure AD-autentisering har valts på VPN-gatewayen. Azurevpnconfig.xml finns i AzureVPN-mappen.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [Om punkt till plats](point-to-site-about.md).
