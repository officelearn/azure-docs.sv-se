---
title: Visar en lista över ditt program i Azure Active Directory-programgalleriet | Microsoft Docs
description: Visa en lista över ett program som stöder enkel inloggning i appgalleriet för Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e02c60d46fe709c8d418ea4743ba383147e9ddac
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Visa ditt program i Azure Active Directory-programgalleriet


##  <a name="what-is-the-azure-ad-application-gallery"></a>Vad är Azure AD application gallery?

Azure Active Directory (AD Azure) är en molnbaserad identitetstjänst. Den [Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/) i Azure Marketplace app store, där alla program kopplingar publiceras för enkel inloggning och användaretablering. Kunder som använder Azure AD som en identitetsleverantör hitta till olika SaaS-program kopplingar som publiceras här. IT-administratörer lägga till kopplingar från appgalleriet och sedan konfigurera och använda kopplingar för enkel inloggning och etablering. Azure AD stöder alla större federation-protokoll för enkel inloggning, inklusive SAML 2.0, OpenID Connect, OAuth och WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Vilka är fördelarna med att visa en lista över ett program i galleriet?

*  Kunder att hitta den bästa möjliga enkel inloggning.

*  Programkonfigurationen är enkel och minimal. 

*  En snabbsökning hittar programmet i galleriet.

*  Frigör Basic, och alla Premium Azure AD-användare kan använda den här integreringen. 

*  Gemensamma kunder få stegvisa configuration självstudiekursen. 

*  Kunder som använder SCIM kan använda etablering för samma app.


##  <a name="prerequisites-implement-federation-protocol"></a>Förutsättningar: Implementera federation-protokollet

Om du vill visa ett program i appgalleriet för Azure AD, måste du först använder du ett av följande federation protokoll som stöds av Azure AD. Läs villkoren för Azure AD application gallery härifrån. 

*   **OpenID Connect**: skapa flera program i Azure AD och genomföra den [Azure AD medgivande framework](active-directory-integrating-applications.md#overview-of-the-consent-framework) för ditt program. Skicka inloggningsbegäran om till en gemensam slutpunkt så att en kund kan ge medgivande till programmet. Du kan styra användaråtkomst baserat på klient-ID och användarens UPN togs emot i token. Om du vill integrera ditt program med Azure AD, följer du de [utvecklare instruktioner](active-directory-authentication-scenarios.md).

*   **SAML 2.0** eller **WS Fed**: ditt program måste ha möjlighet att göra SAML/WS-Fed SSO-integrering i SP eller IDP-läge. Om din app stöder SAML 2.0, kan du integrera den direkt med en Azure AD-klient med hjälp av den [instruktioner för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

*   **Lösenord SSO**: skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenordsbaserade enkel inloggning](../active-directory-appssoaccess-whatis.md). Lösenordsbaserade SSO, även kallat lösenord vaulting, kan du hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto som din organisation sociala medier app konton.

##  <a name="updateremove-existing-listing"></a>Uppdatera/ta bort befintlig registrering

Om du vill uppdatera eller ta bort ett befintligt program i appgalleriet för Azure AD, måste du först begära i den [programmet nätverket Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har en Office 365-konto kan du använda som för att logga in på den här portalen. Om du inte använda ditt Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

* Välj lämpligt alternativ från den under bild

    ![Tidslinjen för registrering saml till gallery](./media/active-directory-app-gallery-listing/updateorremove.png)

    * Om du vill uppdatera ett befintligt program väljer du **uppdatera befintliga program lista**.

    * Om du vill ta bort ett befintligt program från Azure AD-galleriet väljer **ta bort befintliga program lista**

    * Om du har några frågor om åtkomst kan kontakta den [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Skicka begäran på portalen

När du har testat att integrera dina appar fungerar med Azure AD, skicka din begäran om åtkomst på vår [programmet nätverket Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har en Office 365-konto kan du använda som för att logga in på den här portalen. Om du inte använda ditt Microsoft-konto (till exempel Outlook eller Hotmail) för att logga in.

När du har loggat in visas följande sida. Ange en motivering åtkomst i textrutan och välj sedan **åtkomstbegäran**. Vårt team granskar information och du kommer åt därefter. Sedan kan du logga in på portalen och skicka detaljerad begäran för programmet.

Om du har några frågor om åtkomst kan kontakta den [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Åtkomst-begäran på SharePoint-portalen](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tidslinjer
    
Tidslinje för hur du visar en lista över en SAML 2.0 eller ett WS-Fed program i galleriet är 7 – 10 arbetsdagar.

   ![Tidslinjen för registrering saml till gallery](./media/active-directory-app-gallery-listing/timeline.png)

Tidslinje för hur du visar en lista över ett program med OpenID Connect i galleriet är 2 – 5 arbetsdagar.

   ![Tidslinjen för registrering saml till gallery](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskaleringar

Skicka e-post för alla förfrågningar i [Azure AD SSO Integration Team](mailto:SaaSApplicationIntegrations@service.microsoft.com) som är SaaSApplicationIntegrations@service.microsoft.com och vi kommer att svara så snart som möjligt.
