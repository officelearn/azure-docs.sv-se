---
title: Felsöka SAML-baserad enkel inloggning – Azure Active Directory | Microsoft Docs
description: Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 7f653eca0c768cc35df039cbd51153484710d80a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422261"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory

Lär dig att hitta och åtgärda [enkel inloggning](../manage-apps/what-is-single-sign-on.md) problem för program i Azure Active Directory (Azure AD) som stöder [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Innan du börjar
Vi rekommenderar att du installerar den [Mina appar skyddat Inloggningstillägg](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Den här webbläsartillägget gör det enkelt att samla in SAML-begäran och SAML-Svarsinformation som du behöver för att lösa problem med enkel inloggning. Om du inte kan installera tillägget visar den här artikeln hur du löser problem med både med och utan filnamnstillägget installerad.

Ladda ned och installera den Mina appar skyddat Inloggningstillägg genom att använda någon av följande länkar.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testa SAML-baserad enkel inloggning

Så här testar SAML-baserad enkel inloggning mellan AAD och ett målprogram:

1.  Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör eller en annan administratör som har behörighet att hantera program.
2.  I det vänstra bladet klickar du på **Azure Active Directory**, och klicka sedan på **företagsprogram**. 
3.  I listan över företagsprogram, klickar du på programmet som du vill testa enkel inloggning och klicka sedan alternativen på den vänstra Klicka **enkel inloggning**.
4.  Öppna den SAML-baserad enkel inloggning för testning upplevelsen, i den **domän och URL: er** klickar du på avsnittet **Test SAML inställningen**. Du måste fylla ut och spara obligatoriska attribut först om knappen Testa SAML-inställningen är nedtonat.
5.  I den **testa enkel inloggning** bladet använda företagets autentiseringsuppgifter för inloggning till målprogrammet. Du kan logga in som den aktuella användaren eller som en annan användare. Om du loggar in som en annan användare, en uppmaning blir du ombedd att autentisera.

    ![Testa SAML-sidan](./media/howto-v1-debug-saml-sso-issues/testing.png)


Om du har loggat in, har testet skickats. Azure AD utfärdats i det här fallet en SAML-token för svaret till programmet. Programmet som en SAML-token har logga in.

Om du har ett fel på inloggningssidan för företaget eller programmets sida kan du använda något av följande avsnitt för att lösa problemet.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Lös felet logga in på ditt företag på inloggningssidan

När du försöker logga in kan du se ett fel på ditt företag på inloggningssidan. 

![Inloggningsfel](./media/howto-v1-debug-saml-sso-issues/error.png)

Om du vill felsöka det här felet, behöver du ett felmeddelande och SAML-begäran. I Mina appar skyddat Inloggningstillägg denna information samlas in automatiskt och visar vägledning för lösning på Azure AD. 

För att lösa problemet logga in med MyApps Secure Inloggningstillägg installerat:

1.  När ett fel uppstår tillägget omdirigeras du tillbaka till Azure Ad **testa enkel inloggning** bladet. 
2.  På den **testa enkel inloggning** bladet klickar du på **hämta SAML-begäran**. 
3.  Du bör se viss upplösning som vägledning baserat på felet och värdena i SAML-begäran. Läser du igenom informationen.

Att lösa problemet utan att installera MyApps säker inloggning tillägget:

1. Kopiera felmeddelandet i det nedre högra hörnet på sidan. Felmeddelandet innehåller:
    - Ett Korrelations-ID och tidsstämpel. Dessa värden är viktigt när du skapar ett supportärende med Microsoft, eftersom de hjälper tekniker för att identifiera problemet och tillhandahålla en korrekt lösning på problemet.
    - En uppgift som identifierar orsaken till problemet.
2.  Gå tillbaka till Azure AD och hitta den **testa enkel inloggning** bladet.
3.  I textrutan ovan **få vägledning för lösning**, klistra in ett felmeddelande.
3.  Klicka på **få vägledning för lösning** ska visas stegen för att lösa problemet. Riktlinjerna kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder MyApps säker inloggning tillägget, kanske du behöver ett verktyg som [Fiddler](https://www.telerik.com/fiddler) att hämta SAML-begäran och svaret.
4.  Kontrollera mål i SAML-begäran som motsvarar SAML enkel inloggning för tjänstens URL hämtas från Azure Active Directory
5.  Kontrollera utfärdaren i SAML-begäran är samma ID som du har konfigurerat för programmet i Azure Active Directory. Azure AD använder utfärdaren för att hitta ett program i din katalog.
6.  Kontrollera AssertionConsumerServiceURL där programmet förväntas ta emot SAML-token från Azure Active Directory. Du kan konfigurera det här värdet i Azure Active Directory, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Lösa ett fel på programsidan

Du kan logga in och ett felmeddelande visas på sidan i programmet. Detta inträffar när Azure AD utfärdat en token för programmet, men programmet accepterar inte svaret.   

Att lösa problemet:

1. Om programmet är i Azure AD-galleriet kan du kontrollera att du har följt alla steg för att integrera programmet med Azure AD. Integreringsanvisningar för ditt program finns i den [lista över självstudier för integrering av SaaS-program](../saas-apps/tutorial-list.md).
2. Hämta SAML-svar.
    - Om den Mina appar skyddat inloggningstillägg installeras från den **testa enkel inloggning** bladet klickar du på **hämta SAML-svar**.
    - Om tillägget inte är installerad, använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) att hämta SAML-svar. 
3. Lägg märke till de här elementen i svaret SAML-token:
    - Unik identifierare för användaren för NameID-värde och format
    - Anspråk som utfärdats i token
    - Certifikatet som används för att signera token. Information om hur du granskar SAML-svar finns [enkel inloggning SAML-protokoll](single-sign-on-saml-protocol.md).
4. Mer information om SAML-svar finns [enkel inloggning SAML-protokoll](single-sign-on-saml-protocol.md).
5. Nu när du har granskat SAML-svar, se [fel på sidan för ett program när du har registrerat](../manage-apps/application-sign-in-problem-application-error.md) anvisningar om hur du löser problemet. 
6. Om du fortfarande inte kan logga in, kan du be programleverantören vad som saknas från SAML-svar.


## <a name="next-steps"></a>Nästa steg
Nu när enkel inloggning fungerar i ditt program, kan du [automatisera användaretablering och avetablering för SaaS-program](../manage-apps/user-provisioning.md), eller [Kom igång med villkorlig åtkomst](../conditional-access/app-based-conditional-access.md).


