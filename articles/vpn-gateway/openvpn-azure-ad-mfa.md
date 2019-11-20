---
title: 'Aktivera MFA för VPN-användare: Azure AD-autentisering | Microsoft Docs'
description: Aktivera Multi-Factor Authentication för VPN-användare
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 1b506f8439deabd110daaefab36442140e20d0d4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185362"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>Aktivera Multi-Factor Authentication (MFA) för VPN-användare

Du kan aktivera MFA för användare i din Azure AD-klient så att användarna uppmanas att ange en andra Factor Authentication innan åtkomst beviljas.

> [!NOTE]
> Förutsättning: du har konfigurerat en Azure AD-klient enligt beskrivningen i "Konfigurera en klient"-dokument.
>

### <a name="tenant"></a>1. Logga in på Azure Portal och gå till **Azure Active Directory** , **alla användare** och klicka på **Multi-Factor Authentication**


   ![Ny Azure AD-klient](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. Välj den eller de användare som du vill aktivera MFA för och klicka på **Aktivera**

   ![Ny Azure AD-klient](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. gå till **Azure Active Directory** , **företags program**, **alla program** och klicka på **Azure VPN**


   ![Katalog-ID](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. kontrol lera att alternativet har **Aktiver ATS för användare att logga in?** är inställt på Ja. Om du bara vill att de användare som har behörighet till Azure VPN ska kunna logga in måste du ange **användar tilldelningen?** till Ja och annars kan alla användare i AD-klienten ansluta till VPN.

   ![Behörigheter](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
