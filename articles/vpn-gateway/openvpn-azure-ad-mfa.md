---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering'
description: Aktivera multifaktorautentisering för VPN-användare
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 34ef1b73b06870fd4eaabe88147cd98b281c1f11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472357"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera MFA (Azure Multi Factor Authentication) för VPN-användare

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Aktivera autentisering

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Konfigurera inloggningsinställningar

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Alternativ 1 - Åtkomst per användare

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Alternativ 2 - Villkorlig åtkomst

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill ansluta till det virtuella nätverket måste du skapa och konfigurera en VPN-klientprofil. Se [Konfigurera en VPN-klient för P2S VPN-anslutningar](openvpn-azure-ad-client.md).