---
title: Felsöka SAML-baserad enkel inloggning – Azure Active Directory | Microsoft Docs
description: Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: b953d979fc5306372c1e751fbbb8942d19fed03f
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648922"
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

Testa SAML-baserad enkel inloggning mellan Azure AD och ett målprogram:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör eller en annan administratör som har behörighet att hantera program.
1. I det vänstra bladet välj **Azure Active Directory**, och välj sedan **företagsprogram**. 
1. Markera programmet som du vill testa enkel inloggning och klicka sedan alternativ till vänster väljer i listan över företagsprogram, **enkel inloggning**.
1. Öppna SAML-baserad enkel inloggning för testning upplevelsen genom att gå till **testa enkel inloggning** (steg 5). Om den **Test** knappen är nedtonad, måste du först fylla ut och spara obligatoriska attribut i den **SAML grundkonfiguration** avsnittet.
1. I den **testa enkel inloggning** bladet använda företagets autentiseringsuppgifter för inloggning till målprogrammet. Du kan logga in som den aktuella användaren eller som en annan användare. Om du loggar in som en annan användare, en uppmaning blir du ombedd att autentisera.

    ![Testa SAML-sidan](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Om du har loggat in, har testet skickats. Azure AD utfärdats i det här fallet en SAML-token för svaret till programmet. Programmet som en SAML-token har logga in.

Om du har ett fel på inloggningssidan för företaget eller programmets sida kan du använda något av följande avsnitt för att lösa problemet.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Lös felet logga in på ditt företag på inloggningssidan

När du försöker logga in kan du se ett fel på ditt företag på inloggningssidan som liknar följande exempel.

![Inloggningsfel](./media/howto-v1-debug-saml-sso-issues/error.png)

Om du vill felsöka det här felet, behöver du ett felmeddelande och SAML-begäran. I Mina appar skyddat Inloggningstillägg denna information samlas in automatiskt och visar vägledning för lösning på Azure AD. 

### <a name="to-resolve-the-sign-in-error-with-the-myapps-secure-sign-in-extension-installed"></a>För att lösa problemet logga in med MyApps säker inloggning-tillägget installerat

1. När ett fel uppstår tillägget omdirigeras du tillbaka till Azure AD **testa enkel inloggning** bladet. 
1. På den **testa enkel inloggning** bladet väljer **hämta SAML-begäran**. 
1. Du bör se viss upplösning som vägledning baserat på felet och värdena i SAML-begäran.
1. Du ser en **Lösningslänk** knappen för att automatiskt uppdatera konfigurationen i Azure AD för att lösa problemet. Om du inte ser den här knappen, sedan logga in problemet är inte på grund av felaktig konfiguration i Azure AD.

Vi rekommenderar att du använder feedback textrutan för att informera oss om det finns ingen lösning för inloggning-fel.

### <a name="to-resolve-the-error-without-installing-the-myapps-secure-sign-in-extension"></a>Att lösa problemet utan att installera MyApps säker inloggning tillägget

1. Kopiera felmeddelandet i det nedre högra hörnet på sidan. Felmeddelandet innehåller:
    - Ett Korrelations-ID och tidsstämpel. Dessa värden är viktigt när du skapar ett supportärende med Microsoft, eftersom de hjälper tekniker för att identifiera problemet och tillhandahålla en korrekt lösning på problemet.
    - En uppgift som identifierar orsaken till problemet.
1. Gå tillbaka till Azure AD och hitta den **testa enkel inloggning** bladet.
1. I textrutan ovan **få vägledning för lösning**, klistra in ett felmeddelande.
1. Klicka på **få vägledning för lösning** ska visas stegen för att lösa problemet. Riktlinjerna kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder MyApps säker inloggning tillägget, kanske du behöver ett verktyg som [Fiddler](https://www.telerik.com/fiddler) att hämta SAML-begäran och svaret.
1. Kontrollera att mål i SAML-begäran motsvarar SAML enkel inloggning för tjänstens URL hämtas från Azure AD.
1. Kontrollera utfärdaren i SAML-begäran är samma ID som du har konfigurerat för program i Azure AD. Azure AD använder utfärdaren för att hitta ett program i din katalog.
1. Kontrollera AssertionConsumerServiceURL där programmet förväntas ta emot SAML-token från Azure AD. Du kan konfigurera det här värdet i Azure AD, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Lösa ett fel på programsidan

Du kan logga in och ett felmeddelande visas på sidan i programmet. Detta inträffar när Azure AD utfärdat en token för programmet, men programmet accepterar inte svaret.   

Följ dessa steg för att lösa problemet:

1. Om programmet är i Azure AD-galleriet kan du kontrollera att du har följt alla steg för att integrera programmet med Azure AD. Integreringsanvisningar för ditt program finns i den [lista över självstudier för integrering av SaaS-program](../saas-apps/tutorial-list.md).
1. Hämta SAML-svar.
    - Om den Mina appar skyddat inloggningstillägg installeras från den **testa enkel inloggning** bladet klickar du på **hämta SAML-svar**.
    - Om tillägget inte är installerad, använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) att hämta SAML-svar. 
1. Lägg märke till de här elementen i svaret SAML-token:
    - Unik identifierare för användaren för NameID-värde och format
    - Anspråk som utfärdats i token
    - Certifikatet som används för att signera token. 

     Mer information om SAML-svar finns [enkel inloggning SAML-protokoll](single-sign-on-saml-protocol.md).

1. Nu när du har granskat SAML-svar, se [fel på sidan för ett program när du har registrerat](../manage-apps/application-sign-in-problem-application-error.md) anvisningar om hur du löser problemet. 
1. Om du fortfarande inte kan logga in, kan du be programleverantören vad som saknas från SAML-svar.


## <a name="next-steps"></a>Nästa steg

Nu när enkel inloggning fungerar i ditt program, kan du [automatisera användaretablering och avetablering för SaaS-program](../manage-apps/user-provisioning.md) eller [Kom igång med villkorlig åtkomst](../conditional-access/app-based-conditional-access.md).
