---
title: Lista ditt program i Azure Active Directory-programgalleriet | Microsoft Docs
description: Lär dig att visa ett program som stöder enkel inloggning i appgalleriet för Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: e5db7b9bed674011c2922f026c301172f347f53f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666316"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Visa ditt program i Azure Active Directory-programgalleriet


##  <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD-programgalleriet?

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst. Den [Azure AD-programgalleriet](https://azure.microsoft.com/marketplace/active-directory/all/) är i Azure Marketplace app store, där publiceras alla anslutningsappar för programmet för enkel inloggning och etableringen av användare. Kunder som använder Azure AD som identitetsprovider hitta olika SaaS-programanslutningar publicerade här. IT-administratörer lägga till anslutningar från app-galleriet och sedan konfigurera och Använd kopplingar för enkel inloggning och etablering. Azure AD stöder alla större federation-protokoll för enkel inloggning, inklusive SAML 2.0, OpenID Connect, OAuth och WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Vilka är fördelarna med att visa en lista över ett program i galleriet?

*  Kunder att hitta den bästa möjliga enkel inloggning.

*  Konfiguration av programmet är enkel och minimal.

*  En snabbsökning hittar ditt program i galleriet.

*  Kostnadsfri, Basic, och alla Premium Azure AD-kunder kan använda den här integreringen.

*  Gemensamma kunder få en självstudie med stegvisa konfiguration.

*  Kunder som använder SCIM kan använda etablering för samma app.

## <a name="prerequisites"></a>Förutsättningar

- För federerade program (Öppna ID och SAML/WS-Fed), måste programmet stöder SaaS-modellen för att bli listad i Azure AD-galleriet. Galleriet företagsprogram ska ha stöd för kundkonfigurationer med flera och inte en specifik kund.

- För att öppna ID Connect programmet bör vara med flera innehavare och [Azure AD-ramverket för medgivande](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) korrekt bör implementeras för programmet. Användaren kan skicka inloggningsförfrågan till en gemensam slutpunkt så att alla kunder kan ge samtycke till programmet. Du kan styra användaråtkomst baserat på klient-ID och användarens UPN tas emot i token.

- För SAML 2.0/WS-Fed måste ditt program möjlighet att genomföra SAML/WS-Fed SSO-integrering i SP eller IDP-läge. Kontrollera att det fungerar korrekt innan du skickar in begäran.

- För lösenord för enkel inloggning, se till att ditt program stöder formulärautentisering så att lösenordsvalv kan göras för att få enkel inloggning fungerar som förväntat.

- För förfrågningar om etableringen av automatisk användare bör programmet visas i galleriet med funktionen för enkel inloggning som är aktiverad med någon av federation-protokollet som beskrivs ovan. Du kan begära för enkel inloggning och Användaretablering tillsammans på portalen om det inte redan visas.

##  <a name="implementing-sso-using-federation-protocol"></a>Implementera enkel inloggning med federation-protokollet

Om du vill visa ett program i Azure AD-appgalleri, måste du först att implementera en av följande federation protokoll som stöds av Azure AD och godkänner villkoren Azure AD application Gallery. Läs villkoren i Azure AD-programgalleriet från [här](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/).

*   **OpenID Connect**: Om du vill integrera ditt program med Azure AD med öppna ID Connect-protokollet, följer du de [utvecklares instruktioner](authentication-scenarios.md).

    ![Tidslinje för att visa en lista över OpenID Connect-program i galleriet](./media/howto-app-gallery-listing/openid.png)

    * Om du vill lägga till dina program i listan i galleriet med hjälp av OpenID Connect, Välj **OpenID Connect och OAuth 2.0** precis som ovan.

    * Om du har några frågor om åtkomst kan du kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** eller **WS-Fed**: om din app har stöd för SAML 2.0, kan du integrera det direkt med en Azure AD-klient med hjälp av den [anvisningar för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

    ![Tidslinje för att visa en lista över SAML 2.0 eller WS-Fed program i galleriet](./media/howto-app-gallery-listing/saml.png)

    * Om du vill lägga till dina program i listan i galleriet med **SAML 2.0** eller **WS-Fed**väljer **SAMl 2.0/WS-Fed** precis som ovan.

    * Om du har några frågor om åtkomst kan du kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementera enkel inloggning med lösenord för enkel inloggning

Skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenordsbaserad enkel inloggning](../manage-apps/what-is-single-sign-on.md). Lösenordsbaserad SSO, även kallat lösenord vaulting, kan du hantera användaråtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, till exempel till din organisations konton för sociala medier.

![Tidslinje för att visa en lista över lösenord SSO-program i galleriet](./media/howto-app-gallery-listing/passwordsso.png)

* Om du vill lägga till dina program i listan i galleriet med lösenord för enkel inloggning, Välj **lösenord SSO** precis som ovan.

* Om du har några frågor om åtkomst kan du kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Uppdatera/ta bort befintlig registrering

Om du vill uppdatera eller ta bort ett befintligt program i Azure AD app-galleriet, måste du först begära i den [Application nätverk portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har en Office 365-konto kan du använda den för att logga in på den här portalen. Annars kan du använda ditt Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

* Välj lämpligt alternativ från i bilden nedan

    ![Tidslinje för att visa en lista över saml-program i galleriet](./media/howto-app-gallery-listing/updateorremove.png)

    * Om du vill uppdatera ett befintligt program väljer **uppdatera befintliga program lista**.

    * Om du vill ta bort ett befintligt program från Azure AD-galleriet väljer **ta bort befintliga program-lista**

    * Om du har några frågor om åtkomst kan du kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Skicka din begäran i portalen

När du har testat att din programintegrering fungerar med Azure AD, skicka din begäran om åtkomst på vår [Application nätverk portalen](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har en Office 365-konto kan du använda den för att logga in på den här portalen. Annars kan du använda ditt Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

När du har loggat in visas följande sida. Ange en motivering för att ha tillgång i textrutan och välj sedan **begär åtkomst**. Vårt team granskar informationen och ger dig tillgång i enlighet med detta. Efter det kan du logga in på portalen och skicka din detaljerad av programmet.

Om du har några frågor om åtkomst kan du kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Åtkomstbegäran på SharePoint-portalen](./media/howto-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tidslinjer
    
Tidslinje för att visa en lista över en SAML 2.0 eller WS-Fed program i galleriet är 7 – 10 arbetsdagar.

   ![Tidslinje för att visa en lista över saml-program i galleriet](./media/howto-app-gallery-listing/timeline.png)

Tidslinje för att visa en lista över ett OpenID Connect-program i galleriet är 2 – 5 arbetsdagar.

   ![Tidslinje för att visa en lista över saml-program i galleriet](./media/howto-app-gallery-listing/timeline2.png)

Tidslinje för att visa en lista över program i galleriet med stöd för användaretablering är 40-45 dagar.

   ![Tidslinje för att visa en lista över saml-program i galleriet](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Ökningar av

Alla ökningar av, skickar du e-postmeddelande till den [enkel inloggning för Azure AD-integrering Team](mailto:SaaSApplicationIntegrations@service.microsoft.com) som är SaaSApplicationIntegrations@service.microsoft.com och vi svarar så snart som möjligt.