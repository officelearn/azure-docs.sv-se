---
title: "Visar en lista över ditt program i Azure Active Directory-programgalleriet"
description: "Visa en lista över ett program som stöder enkel inloggning i Azure Active Directory-galleriet | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Visar en lista över ditt program i Azure Active Directory-programgalleriet


##  <a name="what-is-azure-ad-app-gallery"></a>Vad är Azure AD app gallery?

Azure AD är en molnbaserad identitetstjänst. [Azure AD app-galleriet](https://azure.microsoft.com/marketplace/active-directory/all/) är en gemensam lagring där alla program kopplingar publiceras för enkel inloggning och användaretablering. Våra ömsesidiga kunder som använder Azure AD som identitetsprovider leta efter olika SaaS-program kopplingar, vilka publiceras här. IT-administratören lägger till koppling från galleriet appen och konfigurerar och använder den för enkel inloggning och etablering. Azure AD stöder alla större federation protokoll som SAML 2.0, OpenID Connect, OAuth och WS-Fed för enkel inloggning. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>Vilka är fördelarna med att visa en lista över program i galleriet?

*  Ge bästa möjliga enkel inloggning till kunder.

*  Enkel och minsta konfiguration av programmet.

*  Kunder kan Sök efter programmet och Sök i galleriet. 

*  Kunder kan använda den här integreringen oavsett Azure AD-SKU lediga, Basic eller Premium.

*  Steg för steg configuration självstudier för gemensamma kunder.

*  Aktivera användare etableringen för samma app om du använder SCIM.


##  <a name="what-are-the-pre-requisites"></a>Vilka är kraven?

Om du vill visa ett program i Azure AD-galleriet måste programmet först implementera ett av de federation protokoll som stöds av Azure AD. Läs villkoren för Azure AD application gallery härifrån. Om du använder: 

*   **OpenID Connect** – skapa flera innehavare program i Azure AD och implementera [Azure AD medgivande framework](active-directory-integrating-applications.md#overview-of-the-consent-framework) för ditt program. Skicka inloggningsbegäran om till vanliga slutpunkten så att en kund kan ge medgivande till programmet. Du kan styra kunden användaråtkomst baserat på klient-ID och användarens UPN togs emot i token. Om du vill integrera ditt program med Azure AD kan du följa den [developer instruktioner](active-directory-authentication-scenarios.md).

*   **SAML 2.0 eller WS-Fed** – ditt program bör ha en möjlighet att göra SAML/WS-Fed SSO-integrering i SP eller IDP-läge. Alla appar som stöder SAML 2.0 kan integreras direkt med en Azure AD-klient med hjälp av den [instruktioner för att lägga till ett anpassat program](../active-directory-saas-custom-apps.md).

*   **Lösenord SSO** – skapa ett webbprogram som har en HTML-inloggningssida konfigurera [lösenordsbaserade enkel inloggning](../active-directory-appssoaccess-whatis.md). Lösenordsbaserade SSO, även kallat lösenord vaulting, kan du hantera åtkomst och lösenord till webbprogram som inte har stöd för identitetsfederation. Det är också användbara för situationer där flera användare behöva dela ett enda konto som din organisation sociala medier app konton. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>Processen för att skicka begäran på portalen

När du har testat att integrera dina appar fungerar med Azure AD, måste du skicka din begäran om åtkomst på vår [programmet nätverket Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Om du har en Office 365-konto kan använda du som att logga in på den här portalen annars använder ditt Microsoft-ID (Live ID, Outlook, Hotmail o.s.v.) för inloggning. Du ser följande sida att begära din åtkomst. Ange en motivering i textrutan och klicka på **åtkomstbegäran**. Vårt team granskar alla detaljer och ge dig åtkomst i enlighet med detta. Sedan kan du logga in på portalen och skicka detaljerad begäran för programmet.

Om du står inför eventuella problem angående åtkomst kontaktar [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Åtkomst-begäran på SharePoint-portalen](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Tidslinjer
    
*   Processen lista SAML 2.0 och WS-Fed programmet till gallery - **7 – 10 arbetsdagar**

   ![Tidslinjen för registrering saml till gallery](./media/active-directory-app-gallery-listing/timeline.png)

*   Processen för att visa en lista över OpenID Connect program till gallery - **2 – 5 arbetsdagar**

   ![Tidslinjen för registrering saml till gallery](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskaleringar

Alla eskaleringar släppa i ett e-postmeddelande till [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) och vi gå tillbaka till du villkoret så SNART.

