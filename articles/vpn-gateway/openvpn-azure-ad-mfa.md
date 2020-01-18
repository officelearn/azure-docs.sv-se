---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering'
description: Aktivera Multi-Factor Authentication för VPN-användare
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169716"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera Azure Multi-Factor Authentication (MFA) för VPN-användare

Om du vill att användarna ska uppmanas att ange en andra faktor innan de beviljas åtkomst, kan du konfigurera Azure Multi-Factor Authentication (MFA) för din Azure AD-klient. Stegen i den här artikeln hjälper dig att aktivera ett krav för tvåstegsverifiering.

## <a name="prereq"></a>Krav

Förutsättningen för den här konfigurationen är en konfigurerad Azure AD-klient med hjälp av stegen i [Konfigurera en klient](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Konfigurera inloggnings inställningar

Konfigurera inloggnings inställningar på sidan **Azure VPN-egenskaper** .

1. Vill du **Aktivera inloggning för användare?** till **Ja**. Detta gör det möjligt för alla användare i AD-klienten att ansluta till VPN-anslutningen.
2. Vill du ange **användar tilldelning krävs?** **Ja** om du vill begränsa inloggningen till endast användare som har behörighet till Azure VPN.
3. Spara ändringarna.

   ![Behörigheter](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Nästa steg

För att ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
