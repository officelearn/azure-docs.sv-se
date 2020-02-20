---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering'
description: Aktivera Multi-Factor Authentication för VPN-användare
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: alzam
ms.openlocfilehash: 34ef1b73b06870fd4eaabe88147cd98b281c1f11
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472357"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera Azure Multi-Factor Authentication (MFA) för VPN-användare

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enableauth"></a>Aktivera autentisering

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="enablesign"></a>Konfigurera inloggnings inställningar

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="peruser"></a>Alternativ 1 – åtkomst per användare

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="conditional"></a>Alternativ 2 – villkorlig åtkomst

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Nästa steg

För att ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).