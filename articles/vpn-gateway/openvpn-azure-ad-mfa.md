---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering'
description: Aktivera Multi-Factor Authentication för VPN-användare
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964754"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera Azure Multi-Factor Authentication (MFA) för VPN-användare

Om du vill att användarna ska uppmanas att ange en andra faktor innan de beviljas åtkomst kan du konfigurera Azure Multi-Factor Authentication (MFA) per användare eller utnyttja Multi-Factor Authentication (MFA) via [villkorlig åtkomst](../active-directory/conditional-access/overview.md) för mer detaljerad kontroll. Att konfigurera Multi-Factor Authentication per användare kan aktive ras utan ytterligare kostnad, men när du aktiverar MFA per användare uppmanas användaren att ange andra Factor Authentication för alla program som är kopplade till Azure AD-klienten. Villkorlig åtkomst ger bättre korn kontroll över hur en andra faktor ska höjas och kan tillåta tilldelning av MFA till enbart VPN och inte andra program som är kopplade till Azure AD-klienten.

## <a name="enableauth"></a>Aktivera autentisering

1. Navigera till **Azure Active Directory-> företags program – > alla program**.
2. På sidan **företags program – alla program** väljer du **Azure VPN**.

   ![Katalog-ID](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurera inloggnings inställningar

Konfigurera inloggnings inställningar på sidan **Azure VPN-egenskaper** .

1. Vill du **Aktivera inloggning för användare?** till **Ja**. Detta gör det möjligt för alla användare i AD-klienten att ansluta till VPN-anslutningen.
2. Vill du ange **användar tilldelning krävs?** **Ja** om du vill begränsa inloggningen till endast användare som har behörighet till Azure VPN.
3. Spara ändringarna.

   ![Behörigheter](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Alternativ 1 – aktivera Multi-Factor Authentication (MFA) via villkorlig åtkomst

Villkorlig åtkomst möjliggör detaljerad åtkomst kontroll för varje program.  Observera att om du vill använda villkorlig åtkomst bör du ha Azure AD Premium 1 eller fler licenser som tillämpas på de användare som ska omfattas av reglerna för villkorlig åtkomst.

1. På sidan **företags program – alla program** väljer du **Azure VPN**, väljer **villkorlig åtkomst**och klickar på **ny princip**.
2. Under användare och grupper, på fliken *Inkludera* TABB **väljer du användare och grupper**, markerar **användare och grupper**och väljer en grupp eller uppsättning användare som ska omfattas av MFA.  Klicka på **Klar**.
![Tilldelningar](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Under **bevilja**, kontrollerar du **bevilja åtkomst**, kontrollerar **kräver Multi-Factor Authentication**, markerar kryss **rutan Kräv alla valda kontroller**och klickar på knappen **Välj** .
![bevilja åtkomst-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Kontrol lera under **Aktivera princip** och **Klicka på knappen** **skapa** .
![aktivera princip](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Alternativ 2 – aktivera Multi-Factor Authentication (MFA) per användare

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Nästa steg

För att ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
