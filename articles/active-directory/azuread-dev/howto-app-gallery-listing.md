---
title: Lista din app i Azure AD-programgalleriet | Microsoft-dokument
description: Lär dig hur du listar ett program som stöder enkel inloggning i Azure Active Directory-appgalleriet
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: fbdae8b8506731f13ff08e877ea8eedb6d57fa78
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666927"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Visa ditt program i Azure Active Directory-programgalleriet

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Den här artikeln visar hur du listar ett program i Azure Active Directory (Azure AD) programgalleriet, implementerar enkel inloggning (SSO) och hanterar listningen.

## <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

- Kunderna hittar den bästa möjliga enkla inloggningsupplevelsen.
- Konfigurationen av programmet är enkel och minimal.
- En snabb sökning hittar ditt program i galleriet.
- Kostnadsfria, grundläggande och Premium Azure AD-kunder kan alla använda den här integreringen.
- Gemensamma kunder får en steg-för-steg-konfigurationshandledning.
- Kunder som använder SYSTEM för[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(Cross-domain Identity Management) kan använda etablering för samma app.

## <a name="prerequisites"></a>Krav

- För federerade program (Open ID och SAML/WS-Fed) måste programmet stödja SaaS-modellen (Software-as-a-Service) för att få listan i Azure AD-appgalleriet. Företagsgalleriprogrammen måste stödja flera kundkonfigurationer och inte någon specifik kund.
- För Open ID Connect måste programmet vara multitenanted och [Azure AD-medgivande ramverket](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) måste implementeras korrekt för programmet. Användaren kan skicka inloggningsbegäran till en gemensam slutpunkt så att alla kunder kan ge sitt samtycke till programmet. Du kan styra användaråtkomst baserat på klient-ID och användarens UPN som tas emot i token.
- För SAML 2.0/WS-Fed måste ditt program ha möjlighet att utföra SAML/WS-Fed SSO-integreringen i SP- eller IDP-läge. Kontrollera att den här funktionen fungerar som den ska innan du skickar begäran.
- För lösenord SSO, se till att ditt program stöder formulärautentisering så att lösenord valv kan göras för att få enkel inloggning för att fungera som förväntat.
- Du behöver ett permanent konto för testning med minst två registrerade användare.

**Hur får man Azure AD för utvecklare?**

Du kan få ett kostnadsfritt testkonto med alla premium-Azure AD-funktioner – 90 dagar gratis och kan utökas så länge du arbetar med det:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Skicka begäran i portalen

När du har testat att programintegreringen fungerar med Azure AD skickar du in din programbegäran i [Microsoft Application Network portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Om följande sida visas när du har loggat in kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Ange det e-postkonto som du vill använda för att skicka begäran. En e-postadress [name@yourbusiness.com](mailto:name@yourbusiness.com) för företag som föredras. Azure AD-teamet lägger till kontot i Microsoft Application Network-portalen.

![Meddelande om åtkomstbegäran på SharePoint-portalen](./media/howto-app-gallery-listing/errorimage.png)

När kontot har lagts till kan du logga in på Microsoft Application Network-portalen.

Om följande sida visas när du har loggat in anger du en affärsmotivering för att behöva åtkomst i textrutan. Välj sedan **Begär åtkomst**.

  ![Rutan Affärsmotivering på SharePoint-portalen](./media/howto-app-gallery-listing/accessrequest.png)

Vårt team granskar detaljerna och ger dig tillgång därefter. När din begäran har godkänts kan du logga in på portalen och skicka begäran genom att välja panelen **Skicka begäran (ISV)** på startsidan.

![Skicka sidan Begär (ISV) på startsidan](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problem med att logga in på portalen

Om du ser det här felet när du loggar in så här är detaljerna i problemet och det är så du kan fixa det.

* Om din inloggning har blockerats enligt nedan:

  ![problem med att lösa program i galleriet](./media/howto-app-gallery-listing/blocked.png)

**Vad händer:**

Gästanvändaren är federerad till en hemklient som också är en Azure AD. Gästanvändaren löper hög risk. Microsoft tillåter inte högriskanvändare att komma åt sina resurser. Alla högriskanvändare (anställda eller gäster/leverantörer) måste åtgärda/stänga sin risk för att komma åt Microsoft-resurser. För gästanvändare kommer den här användarrisken från hemklienten och principen kommer från resursklientinnehavaren (Microsoft i det här fallet).
 
**Säkra lösningar:**

* MFA-registrerade gästanvändare åtgärdar sin egen användarrisk. Detta kan göras av gästanvändaren som utförhttps://aka.ms/sspr) en säker lösenordsändring eller återställning (i deras hemklient (detta behöver MFA och SSPR i hemklienten). Den säkra lösenordsändringen eller återställningen måste initieras på Azure AD och inte on-prem.

* Gästanvändare har sina administratörer åtgärda sin risk. I det här fallet utför administratören en återställning av lösenord (tillfällig lösenordsgenerering). Detta behöver inte identitetsskydd. Gästanvändarens administratör kan gå https://aka.ms/RiskyUsers till och klicka på "Återställ lösenord".

* Gästanvändare har sina administratörer nära / avfärda sin risk. Återigen behöver detta inte identitetsskydd. Admin kan gå https://aka.ms/RiskyUsers till och klicka på "Avfärda användarrisk". Admin måste dock göra due diligence för att säkerställa att detta var en falsk positiv riskbedömning innan du stänger användarrisken. Annars äventyrar de sina och Microsofts resurser genom att undertrycka en riskbedömning utan undersökning.

> [!NOTE]
> Om du har några problem med åtkomst kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementera SSO med hjälp av federationsprotokollet

Om du vill lista ett program i Azure AD-appgalleriet måste du först implementera något av följande federationsprotokoll som stöds av Azure AD. Du måste också godkänna villkoren för Azure AD-programgalleriet. Läs villkoren i Azure AD-programgalleriet på [den här webbplatsen](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect:** Om du vill integrera ditt program med Azure AD med hjälp av Open ID Connect-protokollet följer du [utvecklarnas instruktioner](v1-authentication-scenarios.md).

    ![Lista ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/openid.png)

    * Om du vill lägga till programmet i listan i galleriet med OpenID Connect väljer du **OpenID Connect & OAuth 2.0** som visas.
    * Om du har några problem med åtkomst kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** eller **WS-Fed**: Om din app stöder SAML 2.0 kan du integrera den direkt med en Azure AD-klient genom att följa [instruktionerna för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

  ![Lista en SAML 2.0 eller WS-Fed ansökan i galleriet](./media/howto-app-gallery-listing/saml.png)

  * Om du vill lägga till ditt program i listan i galleriet med hjälp av **SAML 2.0** eller **WS-Fed**väljer du **SAML 2.0/WS-Fed** som visas.

  * Om du har några problem med åtkomst kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementera SSO med hjälp av lösenordet SSO

Skapa ett webbprogram som har en HTML-inloggningssida för att konfigurera [lösenordsbaserad enkel inloggning](../manage-apps/what-is-single-sign-on.md). Lösenordsbaserad SSO, även kallad lösenordsvalv, gör att du kan hantera användaråtkomst och lösenord till webbprogram som inte stöder identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, till exempel till organisationens konton för sociala medier.

![Lista ett lösenord SSO-program i galleriet](./media/howto-app-gallery-listing/passwordsso.png)

* Om du vill lägga till programmet i listan i galleriet med hjälp av lösenord SSO väljer du **Lösenord SSO** som visas.
* Om du har några problem med åtkomst kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Begäran om användaretablering

Följ processen som visas i följande bild för att begära etablering av användare.

   ![Begäran om användaretablering](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Uppdatera eller ta bort en befintlig lista

Om du vill uppdatera eller ta bort ett befintligt program i Azure AD-appgalleriet måste du först skicka begäran i [programnätverksportalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har ett Office 365-konto använder du det för att logga in på den här portalen. Om inte, använd ditt Microsoft-konto, till exempel Outlook eller Hotmail, för att logga in.

- Välj lämpligt alternativ som visas i följande bild.

    ![Lista ett SAML-program i galleriet](./media/howto-app-gallery-listing/updateorremove.png)

    * Om du vill uppdatera ett befintligt program väljer du lämpligt alternativ enligt dina krav.
    * Om du vill ta bort ett befintligt program från Azure AD-appgalleriet väljer du **Ta bort min programlista från galleriet**.
    * Om du har några problem med åtkomst kontaktar du [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Lista begäranden från kunder

Kunder kan skicka en begäran om att få en lista över ett program genom att välja **App-begäranden av kunder** > **Skicka ny begäran**.

![Visar panelen appar som efterfrågas av kunden](./media/howto-app-gallery-listing/customer-submit-request.png)

Här är flödet av kundbestämda program.

![Visar flödet för appar som efterfrågas av kunden](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Tidslinjer

Tidslinjen för processen att lista en SAML 2.0 eller WS-Fed ansökan i galleriet är 7 till 10 arbetsdagar.

  ![Tidslinje för att lista ett SAML-program i galleriet](./media/howto-app-gallery-listing/timeline.png)

Tidslinjen för processen att lista ett OpenID Connect-program i galleriet är 2 till 5 arbetsdagar.

  ![Tidslinje för att lista ett OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Upptrappning

För alla eskaleringar skickar du e-post SaaSApplicationIntegrations@service.microsoft.comtill Azure AD [SSO-integrationsteamet](mailto:SaaSApplicationIntegrations@service.microsoft.com) på , så svarar vi så snart som möjligt.