---
title: SAML enkel inloggning för lokala appar med Azure AD App Proxy
description: Lär dig hur du tillhandahåller enkel inloggning för lokala program som är säkrade med SAML-autentisering. Ge fjärråtkomst till lokala appar med Programproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3d2117e913f292e92f37f31d2e123587c70a189
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803305"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML enkel inloggning för lokala program med Application Proxy

Du kan tillhandahålla enkel inloggning (SSO) till lokala program som är säkrade med SAML-autentisering och ger fjärråtkomst till dessa program via Application Proxy. Med SAML enkel inloggning autentiserar Azure Active Directory (Azure AD) till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggningsinformationen till programmet via ett anslutningsprotokoll. Du kan också mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk. Genom att aktivera Application Proxy utöver SAML SSO, kommer dina användare att ha extern åtkomst till programmet och en sömlös SSO-upplevelse.

Programmen måste kunna använda SAML-token som utfärdats av **Azure Active Directory**. Den här konfigurationen gäller inte för program som använder en lokal identitetsprovider. I de här scenarierna rekommenderar vi att du granskar [resurser för att migrera program till Azure AD](migration-resources.md).

SAML SSO med Application Proxy fungerar också med SAML token krypteringsfunktionen. Mer information finns i [Konfigurera Azure AD SAML-tokenkryptering](howto-saml-token-encryption.md).

Protokolldiagrammen nedan beskriver den enda inloggningssekvensen för både ett SP-initierat flöde (SP-initierat) och ett IdP-initierat flöde (IdP-initierat). Application Proxy fungerar med SAML SSO genom att cachelagra SAML-begäran och svar på och från det lokala programmet.

  ![SAML SP Flöde](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP Flöde](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Skapa ett program och konfigurera SAML SSO

1. I Azure-portalen väljer du **Azure Active Directory > Enterprise-program** och väljer Nytt **program**.

2. Ange visningsnamnet för det nya programmet, välj **Integrera alla andra program som du inte hittar i galleriet**och välj sedan **Skapa**.

3. På appens **översiktssida** väljer du **Enkel inloggning**.

4. Välj **SAML** som enkel inloggningsmetod.

5. Först ställa in SAML SSO att arbeta medan på företagets nätverk. På sidan **Konfigurera enkel inloggning med SAML** går du till rubriken Grundläggande **SAML-konfiguration** och väljer dess **redigeringsikon** (en penna). Följ stegen i [Ange grundläggande SAML-konfiguration](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) för att konfigurera SAML-baserad autentisering för programmet.

6. Lägg till minst en användare i programmet och se till att testkontot har åtkomst till programmet. När du är ansluten till företagsnätverket använder du testkontot för att se om du har enkel inloggning till programmet. 

   > [!NOTE]
   > När du har konfigurerat Programproxy kommer du tillbaka och uppdaterar WEBBADRESSEN FÖR **SAML-svar**.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicera den lokala programmet med Application Proxy

Innan du kan tillhandahålla SSO för lokala program måste du aktivera Programproxy och installera en anslutningsapp. Se självstudien [Lägg till ett lokalt program för fjärråtkomst via Programproxy i Azure AD](application-proxy-add-on-premises-application.md) för att lära dig hur du förbereder din lokala miljö, installerar och registrerar en anslutningsapp och testar kopplingen. Följ sedan dessa steg för att publicera ditt nya program med Application Proxy. Andra inställningar som inte nämns nedan finns i avsnittet [Lägg till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) i självstudien.

1. När programmet fortfarande är öppet i Azure-portalen väljer du **Programproxy**. Ange den **interna URL:en** för programmet. Om du använder en anpassad domän måste du också ladda upp TLS/SSL-certifikatet för ditt program. 
   > [!NOTE]
   > Använd i bästa fall anpassade domäner när det är möjligt för en optimerad användarupplevelse. Läs mer om [att arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

2. Välj **Azure Active Directory** som metod för **förautentisering** för ditt program.

3. Kopiera den **externa URL:en** för programmet. Du behöver den här URL:en för att slutföra SAML-konfigurationen.

4. Försök att öppna programmet med den **externa URL:en** med testkontot för att verifiera att programproxy har konfigurerats korrekt. Om det finns problem läser [du Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Uppdatera SAML-konfigurationen

1. När programmet fortfarande är öppet i **Azure-portalen**väljer du Enkel inloggning . 

2. På sidan **Konfigurera enkel inloggning med SAML** går du till rubriken Grundläggande **SAML-konfiguration** och väljer dess **redigeringsikon** (en penna). Kontrollera att den **externa URL:en** som du har konfigurerat i programproxy är ifylld i fälten **Identifierare,** **Svars-URL**och **Utloggnings-URL.** Dessa url:er krävs för att programproxyn ska fungera korrekt. 

3. Redigera **svars-URL:en** som konfigurerats tidigare så att domänen kan nås på internet via Application Proxy. Om **webbadressen till** exempel `https://contosotravel-f128.msappproxy.net` är och den `https://contosotravel.com/acs`ursprungliga **svarsadressen** var måste `https://contosotravel-f128.msappproxy.net/acs`du uppdatera den ursprungliga **svars-URL:en** till .

    ![Ange grundläggande SAML-konfigurationsdata](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Markera kryssrutan bredvid den uppdaterade **svars-URL:en** för att markera den som standard.

   * När du har markerat den **begärna svars-URL:en** som standard kan du också ta bort den tidigare konfigurerade **svars-URL:en** som använde den interna URL:en.

   * För ett SP-initierat flöde kontrollerar du att backend-programmet anger rätt **svars-URL** eller Url för kontrollförseningstjänsten för att ta emot autentiseringstoken.

    > [!NOTE]
    > Om backend-programmet förväntar sig att **svars-URL:en** ska vara den interna URL:en måste du antingen använda [anpassade domäner](application-proxy-configure-custom-domain.md) för att matcha interna och externa webbadresser eller installera tillägget Säker inloggning i Mina appar på användarnas enheter. Det här tillägget omdirigeras automatiskt till lämplig programproxytjänst. Information om hur du installerar tillägget finns [i Säker inloggningstillägg för Mina appar](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testa din app

När du har slutfört alla dessa steg ska appen vara igång. Så här testar du appen:

1. Öppna en webbläsare och navigera till den **externa URL** som du skapade när du publicerade appen. 
1. Logga in med testkontot som du har tilldelat appen. Du bör kunna ladda programmet och ha SSO i programmet.

## <a name="next-steps"></a>Nästa steg

- [Hur ger Azure AD Application Proxy enkel inloggning?](application-proxy-single-sign-on.md)
- [Felsöka programproxyn](application-proxy-troubleshoot.md)
