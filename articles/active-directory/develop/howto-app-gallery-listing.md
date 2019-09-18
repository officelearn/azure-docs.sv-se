---
title: Lista ditt program i Azure Active Directory program galleriet | Microsoft Docs
description: Lär dig att lista ett program som stöder enkel inloggning i Azure Active Directory app-galleriet
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba4beeff5484f774e801842f06d694af456c4e96
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033757"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Anvisningar: Visa ditt program i Azure Active Directory-programgalleriet

Den här artikeln visar hur du visar en lista över ett program i Azure AD-programgalleriet, implementera enkel inloggning (SSO) och hantera listan.

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

- Kunderna hittar den bästa möjliga inloggnings upplevelsen.
- Programmets konfiguration är enkel och minimal.
- En snabb sökning hittar ditt program i galleriet.
- Kostnads fria, grundläggande och Premium Azure AD-kunder kan använda denna integrering.
- Ömsesidiga kunder får en steg-för-steg-konfigurations guide.
- Kunder som använder SCIM kan använda etablering för samma app.

## <a name="prerequisites"></a>Förutsättningar

- För federerade program (öppna ID och SAML/WS-utfodras) måste programmet ha stöd för SaaS-modellen för att få listade i Azure AD-galleriet. Enterprise Gallery-program bör ha stöd för flera kundkonfigurationer och inte någon specifik kund.

- För öppen ID Connect ska programmet vara Multi-Tenant och [Azure AD medgivande Framework](consent-framework.md) bör implementeras korrekt för programmet. Användaren kan skicka inloggnings förfrågan till en gemensam slut punkt så att alla kunder kan ge sitt medgivande till programmet. Du kan styra användar åtkomst baserat på klient-ID: t och användarens UPN som togs emot i token.

- För SAML 2.0/WS-utfodras måste ditt program ha möjlighet att utföra SAML/WS-utfodras SSO-integrering i SP-eller IDP-läge. Kontrol lera att det fungerar korrekt innan du skickar in begäran.

- För inloggning med lösen ord kontrollerar du att ditt program stöder formulärautentisering så att lösen ords valvet kan utföras för att få enkel inloggning som förväntat.

- Behöver ett permanent konto för testning med minst 2 användare registrerade.

## <a name="submit-the-request-in-the-portal"></a>Skicka begäran i portalen

När du har testat att program integrationen fungerar med Azure AD skickar du din begäran om åtkomst på vår [program nätverks Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har ett Office 365-konto använder du det för att logga in på den här portalen. Om inte, använder du Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

Om följande sida visas efter inloggningen kontaktar du integrerings [teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) och anger det e-postkonto som du vill använda för att skicka begäran. Sedan lägger Azure AD-teamet till kontot i Microsoft-programmets nätverks Portal.

![Åtkomstbegäran på SharePoint-portalen](./media/howto-app-gallery-listing/errorimage.png)

När kontot har lagts till kan du logga in på Microsoft-programmets nätverks Portal.

Och om följande sida visas när du har loggat in, ger du en affärs motivering för att behöva åtkomst i text rutan och väljer sedan **begär åtkomst**.

  ![Åtkomstbegäran på SharePoint-portalen](./media/howto-app-gallery-listing/accessrequest.png)

Vårt team granskar informationen och ger dig åtkomst. När din begäran har godkänts kan du logga in på portalen och skicka begäran genom att klicka på panelen för att **skicka en begäran (ISV)** från start sidan.

![Start sida för SharePoint-portalen](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Om du har problem med åtkomsten kontaktar du [integrerings teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementera SSO med Federations protokoll

Om du vill visa ett program i Azure AD App-galleriet måste du först implementera ett av följande Federations protokoll som stöds av Azure AD och samtycka med villkoren i Azure AD Application Gallery. Läs igenom villkoren i Azure AD-programgalleriet [här](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Om du vill integrera ditt program med Azure AD med hjälp av Open ID Connect-protokollet följer du [anvisningarna för utvecklare](authentication-scenarios.md).

    ![Tids linje som visar hur OpenID ansluter programmet till galleriet](./media/howto-app-gallery-listing/openid.png)

    * Om du vill lägga till programmet i listan i galleriet med OpenID Connect väljer du **OpenID connect & OAuth 2,0** som ovan.
    * Om du har problem med åtkomsten kontaktar du [integrerings teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2,0** eller **WS-utfodras**: Om din app stöder SAML 2,0 kan du integrera den direkt med en Azure AD-klient genom att följa [anvisningarna för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

  ![Tids linje för att lista SAML 2,0 eller WS-utfodras program i galleriet](./media/howto-app-gallery-listing/saml.png)

  * Om du vill lägga till ditt program i listan i galleriet med **SAML 2,0** eller **WS-utfodras**, väljer du **SAML 2.0/WS-utfodras** som ovan.
  * Om du har problem med åtkomsten kontaktar du [integrerings teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementera SSO med enkel inloggning för lösen ord

Skapa ett webb program som har en HTML-inloggnings sida för att konfigurera [lösenordsbaserad enkel inloggning](../manage-apps/what-is-single-sign-on.md). Lösenordsbaserad SSO, som även kallas lösen ords valv, gör att du kan hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations konton för sociala media.

![Tids linje för att visa ett SSO-program för lösen ord i galleriet](./media/howto-app-gallery-listing/passwordsso.png)

* Om du vill lägga till ditt program i listan i galleriet med hjälp av lösen ord för enkel inloggning väljer du **lösen ord för SSO** som ovan.
* Om du har problem med åtkomsten kontaktar du [integrerings teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Uppdatera/ta bort befintlig lista

Om du vill uppdatera eller ta bort ett befintligt program i Azure AD App-galleriet måste du först skicka in begäran i [program nätverks portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har ett Office 365-konto använder du det för att logga in på den här portalen. Om inte, använder du Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

- Välj lämpligt alternativ som du ser i följande bild:

    ![Tids linje för att Visa SAML-program i galleriet](./media/howto-app-gallery-listing/updateorremove.png)

    * Om du vill uppdatera ett befintligt program väljer du **Uppdatera befintlig program lista**.
    * Om du vill ta bort ett befintligt program från Azure AD-galleriet väljer du **ta bort befintlig program lista**.
    * Om du har problem med åtkomsten kontaktar du [integrerings teamet för Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Visar begär Anden från kunder

Kunder kan skicka en begäran om att registrera ett program genom att klicka på **app-begäranden av kunder** -> **skicka in ny begäran**.

![Visar panelen kund begärda appar](./media/howto-app-gallery-listing/customer-submit-request.png)

Nedan visas de program som krävs av kunden,

![Visar flödet för efterfrågade appar för kund](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Tidslinjer

Tids linjen för processen med att lista ett SAML 2,0-eller WS-utfodras program i galleriet är 7-10 arbets dagar.

   ![Tids linje för att Visa SAML-program i galleriet](./media/howto-app-gallery-listing/timeline.png)

Tids linjen för processen med att ange ett OpenID Connect-program i galleriet är 2-5 arbets dagar.

   ![Tids linje för att Visa SAML-program i galleriet](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Förfrågningar

Skicka e-post till SaaSApplicationIntegrations@service.microsoft.com integrerings teamet för [Azure AD SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com) , och vi svarar så snart som möjligt för alla eskaleringar.
