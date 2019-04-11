---
title: SAML enkel inloggning för lokala program med Azure Active Directory Application Proxy (förhandsversion) | Microsoft Docs
description: Lär dig att tillhandahålla enkel inloggning för lokala program som publicerats via programproxy som skyddas med SAML-autentisering.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e103604af7aba2a0ef2e3d0e02a721ae4740c40
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469700"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML enkel inloggning för lokala program med Application Proxy (förhandsversion)

Du kan tillhandahålla enkel inloggning (SSO) till lokala program som är säkrade med SAML-autentisering och ge fjärråtkomst till programmen via programproxy. Med SAML enkel inloggning autentiserar Azure Active Directory (AD Azure) till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar information inloggning till programmet via en anslutningsprotokoll. Du kan också mappa användare till specifika programroller baserat på regler som du definierar i SAML-anspråk. Genom att aktivera programproxyn förutom SAML SSO har användarna extern åtkomst till programmet och en sömlös SSO-upplevelse.

Programmen måste kunna använda SAML-token som utfärdas av **Azure Active Directory**. Den här konfigurationen gäller inte för program som använder en lokal identitetsprovider. Dessa scenarier kan vi rekommenderar att du granskar [resurser för att migrera program till Azure AD](migration-resources.md).

SAML SSO med Application Proxy fungerar även med funktionen för SAML-tokenkryptering. Mer information finns i [konfigurera Azure AD-SAML-tokenkryptering](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicera lokala program med Application Proxy

Innan du kan ange SSO för lokala program, kontrollera att du har aktiverat Application Proxy och du har en anslutningsapp som har installerats. Se [lägga till ett lokalt program för fjärråtkomst via programproxy i Azure AD](application-proxy-add-on-premises-application.md) att lära dig hur.

Tänk på följande när du ska självstudien:

* Publicera dina program enligt anvisningarna i självstudien. Se till att välja **Azure Active Directory** som den **förautentisering** metod för ditt program (steg 4 [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Kopiera den **externa URL: en** för programmet.
* Ett bra tips är att använda anpassade domäner när det är möjligt för en optimerad användarupplevelse. Läs mer om [arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).
* Lägg till minst en användare till programmet och kontrollera att testkonto har åtkomst till dina lokala program. Med test konto testet om du kan komma åt programmet genom att besöka den **externa URL: en** att validera Application Proxy är korrekt konfigurerad. Information om felsökning finns i [felsöka programproxyn problem och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="set-up-saml-sso"></a>Ställ in SAML SSO

1. I Azure-portalen väljer du **Azure Active Directory > företagsprogram** och välj programmet i listan.
1. Från appens **översikt** väljer **enkel inloggning**.
1. Välj **SAML** som metod för enkel inloggning.
1. I den **ange in enkel inloggning med SAML** , redigera den **grundläggande SAML-konfiguration** data, och följ stegen i [RETUR SAML grundkonfiguration](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) att konfigurera SAML-baserad autentisering för programmet.

   * Kontrollera att den **svars-URL** matchar eller är en sökväg under den **externa URL: en** för dina lokala program som du har publicerat via programproxy. Om programmet kräver en annan **svars-URL** för SAML-konfiguration, lägger du till detta som den **första** URL i listan och håll den **externa URL: en** som en ytterligare URL sorterade efter först.
   * Se till att programmet också anger rätt **svars-URL** eller URL för Konsumenttjänst för försäkran att använda för att ta emot autentiseringstoken.

     ![Ange grundläggande SAML-konfigurationsdata](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Om backend-applikationer förväntar sig den **svars-URL** för att vara den interna URL: en, måste du installera Mina appar säker inloggning tillägget på användarnas enheter. Det här tillägget omdirigeras automatiskt till lämplig Application Proxy-tjänsten. Om du vill installera tillägget, se [Mina appar skyddat inloggningstillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Testa din app

När du har slutfört de här stegen, men det är din app ska vara igång. Att testa appen:

1. Öppna en webbläsare och navigera till den externa URL: en som du skapade när du har publicerat appen. 
1. Logga in med kontot test som du tilldelade till appen. Du ska kunna läsa in programmet och använda enkel inloggning till programmet.

## <a name="next-steps"></a>Nästa steg

- [Hur tillhandahåller Azure AD Application Proxy enkel inloggning?](application-proxy-single-sign-on.md)
- [Felsöka programproxyn](application-proxy-troubleshoot.md)
