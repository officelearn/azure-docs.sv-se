---
title: Lista ditt program i Azure Active Directory program galleriet | Microsoft Docs
description: Lär dig att lista ett program som stöder enkel inloggning i Azure Active Directory app-galleriet
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e1763b8d6402a6093499f1f06253fe4c7502255
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842786"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Visa ditt program i Azure Active Directory-programgalleriet

Den här artikeln visar hur du visar en lista över ett program i program galleriet Azure Active Directory (Azure AD), implementera enkel inloggning (SSO) och hantera listan.

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

- Kunderna hittar den bästa möjliga inloggnings upplevelsen.
- Programmets konfiguration är enkel och minimal.
- En snabb sökning hittar ditt program i galleriet.
- Kostnads fria, grundläggande och Premium Azure AD-kunder kan använda denna integrering.
- Ömsesidiga kunder får en steg-för-steg-konfigurations guide.
- Kunder som använder systemet för[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(Cross-Domain Identity Management) kan använda etablering för samma app.

## <a name="prerequisites"></a>Krav

- För federerade program (öppna ID och SAML/WS-utfodras) måste programmet ha stöd för SaaS-modellen (Software-as-a-Service) för att få listas i Azure AD App-galleriet. Enterprise Gallery-programmen måste ha stöd för flera kundkonfigurationer och inte någon specifik kund.
- För Open-ID Connect måste programmet vara Multiklient och [Azure AD medgivande Framework](consent-framework.md) måste implementeras korrekt för programmet. Användaren kan skicka inloggnings förfrågan till en gemensam slut punkt så att alla kunder kan ge sitt medgivande till programmet. Du kan styra användar åtkomst baserat på klient-ID: t och användarens UPN som togs emot i token.
- För SAML 2.0/WS-utfodras måste ditt program ha möjlighet att göra SAML/WS-utfodras SSO-integrering i SP-eller IDP-läge. Kontrol lera att den här funktionen fungerar korrekt innan du skickar in begäran.
- För inloggning med lösen ord kontrollerar du att ditt program stöder formulärautentisering så att lösen ords valvet kan göras för att få enkel inloggning att fungera som förväntat.
- Du behöver ett permanent konto för testning med minst två användare registrerade.

## <a name="submit-the-request-in-the-portal"></a>Skicka begäran i portalen

När du har testat att program integrationen fungerar med Azure AD skickar du din begäran om åtkomst i [program nätverks portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har ett Office 365-konto använder du det för att logga in på den här portalen. Om inte, använder du Microsoft-konto, t. ex. Outlook eller Hotmail, för att logga in.

Om följande sida visas när du har loggat in, kontaktar du [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Ange det e-postkonto som du vill använda för att skicka begäran. Azure AD-teamet kommer att lägga till kontot i Microsoft-programmets nätverks Portal.

![Åtkomstbegäran på SharePoint-portalen](./media/howto-app-gallery-listing/errorimage.png)

När kontot har lagts till kan du logga in på Microsoft-programmets nätverks Portal.

Om följande sida visas när du har loggat in, ger du en affärs motivering för att få åtkomst i text rutan. Välj sedan **begär åtkomst**.

  ![Affärs justerings ruta på SharePoint-portalen](./media/howto-app-gallery-listing/accessrequest.png)

Vårt team granskar informationen och ger dig åtkomst. När din begäran har godkänts kan du logga in på portalen och skicka begäran genom att välja panelen för att **skicka begäran (ISV)** på Start sidan.

![Sändnings panelen för begäran (ISV) på Start Sidan](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementera SSO med hjälp av Federations protokollet

Om du vill visa ett program i Azure AD App-galleriet måste du först implementera ett av följande Federations protokoll som stöds av Azure AD. Du måste också godkänna villkoren för Azure AD Application Gallery. Läs villkoren i program galleriet för Azure AD på [den här webbplatsen](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: om du vill integrera ditt program med Azure AD med hjälp av Open ID Connect-protokollet följer du [utvecklarnas instruktioner](v1-authentication-scenarios.md).

    ![Visa ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/openid.png)

    * Om du vill lägga till programmet i listan i galleriet med hjälp av OpenID Connect väljer du **OpenID connect & OAuth 2,0** som det visas.
    * Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** eller **WS-utfodras**: om din app stöder SAML 2,0 kan du integrera den direkt med en Azure AD-klient genom att följa [anvisningarna för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

  ![Visar ett SAML 2,0-eller WS-utfodras program i galleriet](./media/howto-app-gallery-listing/saml.png)

  * Om du vill lägga till ditt program i listan i galleriet med **saml 2,0** eller **WS-utfodras**väljer du **SAML 2.0/WS-utfodras** som visas.
  * Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementera SSO med hjälp av lösen ords inloggning

Skapa ett webb program som har en HTML-inloggnings sida för att konfigurera [lösenordsbaserad enkel inloggning](../manage-apps/what-is-single-sign-on.md). Lösenordsbaserad SSO, som även kallas lösen ords valv, gör att du kan hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations appar för sociala media.

![Visar en lista med ett SSO-program i galleriet](./media/howto-app-gallery-listing/passwordsso.png)

* Om du vill lägga till ditt program i listan i galleriet med hjälp av lösen ord för enkel inloggning väljer du **lösen ord för inloggning** som visas.
* Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Begäran om användar etablering

Följ processen som visas i följande bild för att begära användar etablering.

   ![Begäran om användar etablering](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Uppdatera eller ta bort en befintlig lista

Om du vill uppdatera eller ta bort ett befintligt program i Azure AD App-galleriet måste du först skicka in begäran i [program nätverks portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har ett Office 365-konto använder du det för att logga in på den här portalen. Om inte, använder du Microsoft-konto, t. ex. Outlook eller Hotmail, för att logga in.

- Välj lämpligt alternativ som du ser i följande bild.

    ![Lista ett SAML-program i galleriet](./media/howto-app-gallery-listing/updateorremove.png)

    * Om du vill uppdatera ett befintligt program väljer du lämpligt alternativ enligt ditt krav.
    * Om du vill ta bort ett befintligt program från Azure AD App-galleriet väljer du **ta bort min program listning från galleriet**.
    * Om du har problem med åtkomst kan du kontakta [Azure AD SSO integration-teamet](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Lista begär Anden från kunder

Kunder kan skicka en begäran om att lista ett program genom att välja **app-begäranden från kunder** > **skicka in ny begäran**.

![Visar panelen kund begärda appar](./media/howto-app-gallery-listing/customer-submit-request.png)

Här är ett flöde av kund begärda program.

![Visar flödet kundens begärda appar](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Tidslinjer

Tids linjen för processen med att lista ett SAML 2,0-eller WS-utfodras program i galleriet är 7 till 10 arbets dagar.

  ![Tids linje för att visa ett SAML-program i galleriet](./media/howto-app-gallery-listing/timeline.png)

Tids linjen för processen med att ange ett OpenID Connect-program i galleriet är 2 till 5 arbets dagar.

  ![Tids linje för att visa ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Förfrågningar

För alla eskaleringar skickar du e-post till [Azure AD SSO integration-teamet](mailto:SaaSApplicationIntegrations@service.microsoft.com) på SaaSApplicationIntegrations@service.microsoft.comoch vi svarar så snart som möjligt.
