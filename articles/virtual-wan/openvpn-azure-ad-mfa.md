---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering'
description: Aktivera Multi-Factor Authentication för VPN-användare
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 9329ac33a771c829b8d67670bc07d9a353c58ff1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930582"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera Azure Multi-Factor Authentication (MFA) för VPN-användare

Om du vill att användarna ska uppmanas att ange en andra faktor innan de beviljas åtkomst, kan du konfigurera Azure Multi-Factor Authentication (MFA) för din Azure AD-klient. Stegen i den här artikeln hjälper dig att aktivera ett krav för tvåstegsverifiering.

## <a name="prereq"></a>Krav

Förutsättningen för den här konfigurationen är en konfigurerad Azure AD-klient med hjälp av stegen i [Konfigurera en klient](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Öppna sidan MFA

1. Logga in på Azure Portal.
2. Navigera till **Azure Active Directory – > alla användare**.
3. Välj **Multi-Factor Authentication** för att öppna sidan Multi-Factor Authentication.

   ![Logga in](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Välj användare

1. På sidan **Multi-Factor Authentication** väljer du de användare som du vill aktivera MFA för.
2. Välj **Aktivera**.

   ![Välj](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Aktivera autentisering

1. Navigera till **Azure Active Directory-> företags program – > alla program**.
2. På sidan **företags program – alla program** väljer du **Azure VPN**.

   ![Katalog-ID](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurera inloggnings inställningar

Konfigurera inloggnings inställningar på sidan **Azure VPN-egenskaper** .

1. Vill du **Aktivera inloggning för användare?** till **Ja**. Detta gör det möjligt för alla användare i AD-klienten att ansluta till VPN-anslutningen.
2. Vill du ange **användar tilldelning krävs?** **Ja** om du vill begränsa inloggningen till endast användare som har behörighet till Azure VPN.
3. Spara ändringarna.

   ![Behörigheter](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Nästa steg

För att ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera Azure AD-autentisering för punkt-till-plats-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md).
