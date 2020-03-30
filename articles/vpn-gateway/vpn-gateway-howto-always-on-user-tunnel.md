---
title: Konfigurera en VPN-användartunnel alltid på
titleSuffix: Azure VPN Gateway
description: I den här artikeln beskrivs hur du konfigurerar en alltid på VPN-användartunnel för din VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502270"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurera en tunnel för Always On VPN-användare

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurera en gateway

 Använd instruktionerna i artikeln [Konfigurera en punkt-till-plats-VPN-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att konfigurera VPN-gatewayen så att den använder IKEv2 och certifikatbaserad autentisering.

## <a name="configure-a-user-tunnel"></a>Konfigurera en användartunnel

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Så här tar du bort en profil

Så här tar du bort en profil:

1. Kör följande kommando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppla från anslutningen och avmarkera kryssrutan **Anslut automatiskt.**

   ![Rensa](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Nästa steg

Information om felsökning av anslutningsproblem som kan uppstå finns i [Azure point-to-site-anslutningsproblem](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
