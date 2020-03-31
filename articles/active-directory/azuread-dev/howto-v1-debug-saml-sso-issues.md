---
title: Felsöka SAML-baserad enkel inloggning – Azure Active Directory | Microsoft-dokument
description: Felsök SAML-baserad enkel inloggning till program i Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154856"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Lär dig hur du hittar och åtgärdar [problem med enkel inloggning](../manage-apps/what-is-single-sign-on.md) för program i Azure Active Directory (Azure AD) som stöder [SAML 2.0 (Security Assertion Markup Language).](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du installerar [tillägget För säker inloggning för Mina appar](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Detta webbläsartillägg gör det enkelt att samla in SAML-begäran och SAML-svarsinformation som du behöver för att lösa problem med enkel inloggning. Om du inte kan installera tillägget visar den här artikeln hur du löser problem både med och utan tillägget installerat.

Om du vill hämta och installera tillägget För säker inloggning i Mina appar använder du någon av följande länkar.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testa SAML-baserad enkel inloggning

Så här testar du SAML-baserad enkel inloggning mellan Azure AD och ett målprogram:

1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör eller annan administratör som har behörighet att hantera program.
1. I det vänstra bladet väljer du **Azure Active Directory**och väljer sedan **Enterprise-program**. 
1. I listan över företagsprogram väljer du det program som du vill testa enkel inloggning för och väljer sedan **Enkel inloggning**från alternativen till vänster .
1. Om du vill öppna den SAML-baserade testupplevelsen för enkel inloggning går du till **Testa enkel inloggning** (steg 5). Om **testknappen** är nedtonad måste du fylla i och spara de attribut som krävs först i avsnittet **Grundläggande SAML-konfiguration.**
1. I **bladet Testa enkel inloggning** använder du företagets autentiseringsuppgifter för att logga in på målprogrammet. Du kan logga in som aktuell användare eller som en annan användare. Om du loggar in som en annan användare uppmanas du att autentisera en uppmaning.

    ![Skärmbild som visar testet SAML SSO-sida](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Om du har loggat in har testet godkänts. I det här fallet utfärdade Azure AD en SAML-svarstoken till programmet. Programmet använde SAML-token för att logga in dig.

Om du har ett fel på företagets inloggningssida eller programmets sida använder du något av nästa avsnitt för att lösa felet.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Lösa ett inloggningsfel på företagets inloggningssida

När du försöker logga in kan du se ett fel på företagets inloggningssida som liknar följande exempel.

![Exempel som visar ett fel på företagets inloggningssida](./media/howto-v1-debug-saml-sso-issues/error.png)

Om du vill felsöka det här felet behöver du felmeddelandet och SAML-begäran. Tillägget För säker inloggning för mina appar samlar automatiskt in den här informationen och visar lösningsvägledning på Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Så här löser du inloggningsfelet med det säkra inloggningstillägget För mina appar installerat

1. När ett fel inträffar omdirigerar tillägget dig tillbaka till azure **AD-testets enda** inloggningsblad.
1. På **det enkla inloggningsbladet testa** väljer du **Hämta SAML-begäran**.
1. Du bör se specifika lösningsvägledningar baserat på felet och värdena i SAML-begäran.
1. Du kommer att se en **Fix it-knapp** för att automatiskt uppdatera konfigurationen i Azure AD för att lösa problemet. Om du inte ser den här knappen beror inloggningsproblemet inte på en felkonfiguration på Azure AD.

Om det inte finns någon lösning på inloggningsfelet föreslår vi att du använder textrutan feedback för att informera oss.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Så här löser du problemet utan att installera tillägget För säker inloggning i Mina appar

1. Kopiera felmeddelandet längst ned till höger på sidan. Felmeddelandet innehåller:
    - Ett CorrelationID och tidsstämpel. Dessa värden är viktiga när du skapar ett supportärende med Microsoft eftersom de hjälper teknikerna att identifiera ditt problem och ge en korrekt lösning på problemet.
    - En sats som identifierar orsaken till problemet.
1. Gå tillbaka till Azure AD och hitta **testbladet för enkel inloggning.**
1. Klistra in felmeddelandet i textrutan ovan **Hämta lösningsvägledning.**
1. Klicka på **Hämta lösningsvägledning** om du vill visa steg för att lösa problemet. Vägledningen kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder tillägget För säker inloggning i Mina appar kan du behöva ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att hämta SAML-begäran och svar.
1. Kontrollera att målet i SAML-begäran motsvarar SAML Single Sign-On Service URL som hämtas från Azure AD.
1. Kontrollera att utfärdaren i SAML-begäran är samma identifierare som du har konfigurerat för programmet i Azure AD. Azure AD använder utfärdaren för att hitta ett program i katalogen.
1. Verifiera AssertionConsumerServiceURL är där programmet förväntar sig att ta emot SAML-token från Azure AD. Du kan konfigurera det här värdet i Azure AD, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Lösa ett inloggningsfel på programsidan

Du kan logga in och sedan se ett fel på programmets sida. Detta inträffar när Azure AD utfärdade en token till programmet, men programmet accepterar inte svaret.

Så här löser du felet:

1. Om programmet finns i Azure AD Gallery kontrollerar du att du har följt alla steg för att integrera programmet med Azure AD. Information om hur du hittar integrationsinstruktionerna för ditt program finns i [listan över självstudier för integrering av SaaS-program](../saas-apps/tutorial-list.md).
1. Hämta SAML-svaret.
    - Om tillägget Säker inloggning för Mina appar är installerat klickar du på **hämta SAML-svaret**från det **enskilda inloggningsbladet Testa.**
    - Om tillägget inte är installerat använder du ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att hämta SAML-svaret.
1. Lägg märke till dessa element i SAML-svarstoken:
   - Användarens unika identifierare för NameID-värde och format
   - Anspråk som utfärdats i token
   - Certifikat som används för att signera token.

     Mer information om SAML-svaret finns i [SAML-protokollet för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Nu när du har granskat SAML-svaret läser du [Fel på ett programs sida efter inloggning](../manage-apps/application-sign-in-problem-application-error.md) för vägledning om hur du löser problemet. 
1. Om du fortfarande inte kan logga in kan du fråga programleverantören vad som saknas i SAML-svaret.

## <a name="next-steps"></a>Nästa steg

Nu när enkel inloggning fungerar till ditt program kan du [automatisera användaretablering och avetablering till SaaS-program](../manage-apps/user-provisioning.md) eller [komma igång med villkorlig åtkomst](../conditional-access/app-based-conditional-access.md).
