---
title: Felsöka SAML-baserade enkel inloggning – Azure Active Directory | Microsoft Docs
description: Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory.
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
ms.openlocfilehash: 8bb0df567fbac6e8f8a2e2f64f868b4f219e05ac
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751402"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory

Lär dig att hitta och åtgärda [enkel inloggning](../manage-apps/what-is-single-sign-on.md) problem för program i Azure Active Directory (AD Azure) som stöder [Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Innan du börjar
Vi rekommenderar att du installerar den [Mina appar säker inloggning tillägget](../active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension). Den här webbläsartillägget gör det enkelt att samla in SAML-begäran och SAML-Svarsinformation som du behöver för att lösa problem med enkel inloggning. Om du inte kan installera tillägget visar den här artikeln hur man löser problem både med och utan filnamnstillägget installerad.

Använd någon av följande länkar om du vill hämta och installera Mina appar säker inloggning Extension.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Kant](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testa SAML-baserade enkel inloggning

Så här testar SAML-baserade enkel inloggning mellan AAD och ett målprogram:

1.  Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör eller en annan administratör som har behörighet att hantera program.
2.  I det vänstra bladet, klickar du på **Azure Active Directory**, och klicka sedan på **företagsprogram**. 
3.  Klicka på programmet som du vill testa enkel inloggning och sedan från alternativen vänster klickar du på listan över företagsprogram och **enkel inloggning**.
4.  Öppna den SAML-baserade enkel testning inloggning, i den **domän och URL: er** Klicka på avsnittet **Test SAML inställningen**. Om knappen Testa SAML-inställningen är nedtonat måste du först fylla ut och spara attributen som krävs.
5.  I den **testa enkel inloggning** bladet använda företagets autentiseringsuppgifter för inloggning till målprogrammet. Du kan logga in som den aktuella användaren eller en annan användare. Om du loggar in som en annan användare uppmanas du att autentisera i en kommandotolk.

    ![SAML testsida](media/active-directory-saml-debugging/testing.png)


Om du har loggat in, har testet skickats. Azure AD utfärdats i det här fallet en SAML-token för svar till programmet. Programmet använde SAML-token för att kunna logga in.

Om du har ett fel på sidan för företag eller programmets sidan Använd nästa avsnitt för att åtgärda felet.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Åtgärda ett fel på sidan för företag

När du försöker logga in kan du se ett fel på sidan för företaget. 

![Logga in fel](media/active-directory-saml-debugging/error.png)

Om du vill felsöka det här felet, behöver du ett felmeddelande och SAML-begäran. Mina appar säker inloggning tillägget denna information samlas in automatiskt och visar upplösning vägledning om Azure AD. 

Problemet inloggning med MyApps säker inloggning tillägg installerats:

1.  När ett fel uppstår tillägget omdirigeras du till Azure Ad **testa enkel inloggning** bladet. 
2.  På den **testa enkel inloggning** bladet, klickar du på **hämta begäran om SAML**. 
3.  Du bör se viss upplösning som vägledning baserat på felet och värdena i SAML-begäran. Granska riktlinjerna.

Så här löser felet utan att installera MyApps säker inloggning tillägg:

1. Kopiera felmeddelandet i det nedre högra hörnet på sidan. Felmeddelandet innehåller:
    - Ett CorrelationID och en tidsstämpel. Dessa värden är viktigt när du skapar ett supportärende hos Microsoft, eftersom de hjälper tekniker att identifiera problemet och ange en korrekt lösning på problemet.
    - En uppgift som identifierar orsaken till problemet.
2.  Gå tillbaka till Azure AD och Sök efter den **testa enkel inloggning** bladet.
3.  I rutan ovan **få upplösning vägledning**, klistra in felmeddelandet.
3.  Klicka på **få upplösning vägledning** ska visas stegen för att lösa problemet. Anvisningarna kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder MyApps säker inloggning tillägget kan du behöva ett verktyg som [Fiddler](http://www.telerik.com/fiddler) att hämta SAML-begäran och svar.
4.  Kontrollera mål i SAML-begäran motsvarar SAML enkel inloggning tjänstens Webbadress från Azure Active Directory
5.  Kontrollera utfärdaren i SAML-begäran är samma ID som du har konfigurerat för programmet i Azure Active Directory. Azure AD används utfärdaren för att hitta ett program i din katalog.
6.  Kontrollera AssertionConsumerServiceURL där programmet förväntas ta emot SAML-token från Azure Active Directory. Du kan konfigurera det här värdet i Azure Active Directory, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Åtgärda ett fel på sidan program

Du kan logga in och ett felmeddelande visas på sidan i programmet. Detta inträffar när Azure AD utfärdade token till programmet, men programmet tar inte emot svaret.   

Så här löser du felet:

1. Om programmet är i Azure AD-galleriet, kontrollerar du att du har följt alla steg för att integrera programmet med Azure AD. Integration anvisningarna för ditt program finns i [lista över SaaS-program integration självstudier](../saas-apps/tutorial-list.md).
2. Hämta SAML-svaret.
    - Om tillägget Mina appar säker inloggning installeras från den **testa enkel inloggning** bladet, klickar du på **hämta SAML-svaret**.
    - Om tillägget inte är installerad, använda ett verktyg som [Fiddler](http://www.telerik.com/fiddler) att hämta SAML-svaret. 
3. Lägg märke till de här elementen i svaret SAML-token:
    - Användaren Unik identifierare för NameID värde och format
    - Anspråk som utfärdats i token
    - Certifikatet som används för att signera token. Mer information om hur du granskar SAML-svar finns [enkel inloggning SAML protokoll](active-directory-single-sign-on-protocol-reference.md).
4. Läs mer i SAML-svaret [enkel inloggning SAML protokoll](active-directory-single-sign-on-protocol-reference.md).
5. Nu när du har granskat SAML-svar finns [fel på sidan för ett program när du har registrerat](../application-sign-in-problem-application-error.md) anvisningar om hur du löser problemet. 
6. Om du fortfarande inte kan logga in på har be du leverantören av tillämpningsprogrammet vad saknas i SAML-svaret.


## <a name="next-steps"></a>Nästa steg
Nu när enkel inloggning fungerar i tillämpningsprogrammet kunde [automatisera användaretablering och avetablering för SaaS-program](../active-directory-saas-app-provisioning.md), eller [Kom igång med villkorlig åtkomst](../active-directory-conditional-access-azure-portal-get-started.md).


