---
title: Konfigurera en tunnel för alltid-on VPN-användare
titleSuffix: Azure VPN Gateway
description: Den här artikeln beskriver hur du konfigurerar en Always on VPN-tunnel för din VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502270"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurera en tunnel för Always On VPN-användare

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurera en gateway

 Använd anvisningarna i artikeln [Konfigurera en punkt-till-plats-VPN-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att konfigurera VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering.

## <a name="configure-a-user-tunnel"></a>Konfigurera en användar tunnel

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Använd följande steg för att ta bort en profil:

1. Kör följande kommando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppla från anslutningen och avmarkera kryss rutan **Anslut automatiskt** .

   ![Rensa](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker anslutnings problem som kan uppstå finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
