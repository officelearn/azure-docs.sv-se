---
title: Admin-guide för Azure Active Directory SSO plugin | Microsoft Docs
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jira/växer samman.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Admin-guide för Azure Active Directory SSO plugin-program

## <a name="overview"></a>Översikt

I Azure Active Directory (AD Azure) enkel inloggning (SSO) plugin-programmet Microsoft Azure AD ger kunder möjlighet att använda sina arbets- eller skolkonto konto för inloggning till Atlassian Jira och antal samverkande Server-baserade produkter. Den implementerar SAML 2.0-baserade enkel inloggning.

## <a name="how-it-works"></a>Så här fungerar det

När du vill logga in på programmet Atlassian Jira eller växer samman de finns på **inloggning med Azure AD** på sidan logga in. När de väljer det, är de krävs för att logga in med hjälp av Azure AD organisation inloggningssida visas (det vill säga sina arbets- eller skolkonto-konto).

När användarna autentiseras ska de kunna logga in till programmet. Om de redan har autentiserats med ID och lösenord för sitt arbets- eller skolkonto konto de direkt och logga sedan in till programmet. 

Logga in fungerar över Jira och växer samman. Om användare är inloggad på Jira program och antal samverkande öppnas i samma webbläsarfönster, behöver de ange autentiseringsuppgifter för den andra appen. 

Användare kan också få Atlassian-produkten via Mina appar under arbets-eller skolkonto. De ska logga in utan att ange autentiseringsuppgifter.

> [!NOTE]
> Användaretablering görs inte via plugin-programmet.

## <a name="audience"></a>Målgrupp

Jira och antal samverkande administratörer kan använda plugin-programmet för att aktivera enkel inloggning med hjälp av Azure AD.

## <a name="assumptions"></a>Antaganden

* Jira och antal samverkande instanserna är HTTPS-aktiverade.
* Användare har redan skapats i Jira eller växer samman.
* Användare har roller i Jira eller växer samman.
* Administratörer har åtkomst till information som krävs för att konfigurera plugin-programmet.
* Jira eller växer samman finns utanför företagets nätverk.
* Plugin-program fungerar med endast den lokala versionen av Jira och växer samman.

## <a name="prerequisites"></a>Krav

Observera följande information innan du installerar plugin-programmet:

* Jira och antal samverkande är installerade på Windows 64-bitarsversionen.
* Jira och antal samverkande är HTTPS-aktiverade.
* Jira och antal samverkande finns på internet.
* Admin-autentiseringsuppgifter är för Jira och växer samman.
* Admin-autentiseringsuppgifter är på plats för Azure AD.
* WebSudo har inaktiverats i Jira och växer samman.

## <a name="supported-versions-of-jira-and-confluence"></a>Versioner som stöds av Jira och växer samman

Plugin-programmet stöder följande versioner av Jira och växer samman:

* Jira Core- och programvara: 6.0 7.2.0
* Jira helpdesk: 3.0 och 3.2
* Antal samverkande: 5.0 till 5.10

## <a name="installation"></a>Installation

Följ dessa steg om du vill installera plugin-programmet:

1. Logga in på din Jira eller växer samman instans som en administratör.
    
2. Gå till Jira/antal samverkande-administrationskonsolen och välj **tillägg**.
    
3. Sök efter från Atlassian Marketplace **Microsoft SAML SSO Plugin**.
 
   Lämplig version av plugin-programmet visas i sökresultaten.
 
5. Välj plugin-programmet och universella plugin Manager (finns på) installeras den.
 
När plugin-programmet installeras, visas den i den **användaren installerat tillägg** avsnitt i **Hantera tillägg**.
    
## <a name="plug-in-configuration"></a>Konfiguration av pluginprogrammet

Innan du börjar använda plugin-programmet, måste du konfigurera den. Plugin-program, väljer den **konfigurera** knappen och ange konfigurationsinformationen.

Följande bild visar skärm för konfiguration i både Jira och växer samman:
    
![Konfiguration av pluginprogrammet skärmen](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **URL för tjänstmetadata**: URL: en att hämta federationsmetadata från Azure AD.
 
*   **Identifierare**: URL: en som Azure AD använder för att verifiera källan för begäran. Den mappar till den **identifierare** element i Azure AD. Plugin-programmet automatiskt härleds Webbadressen som https://*< domän: port >*/.
 
*   **Reply URL**: reply-URL: en i din identitetsprovider (IdP) som initierar SAML-inloggning. Den mappar till den **Reply URL** element i Azure AD. Plugin-programmet automatiskt härleds Webbadressen som https://*< domän: port >*/plugins/servlet/saml/auth.
 
*   **Logga URL**: URL inloggning i din IdP som initierar SAML-inloggning. Den mappar till den **inloggning** element i Azure AD. Plugin-programmet automatiskt härleds Webbadressen som https://*< domän: port >*/plugins/servlet/saml/auth.
 
*   **IdP enhets-ID**: enhets-ID som din IdP använder. Den här rutan fylls när metadata-URL är löst.
 
*   **Inloggnings-URL**: Logga in URL: en från din IdP. Den här rutan är ifylld från Azure AD när metadata-URL är löst.
 
*   **Logga ut URL**: Logga ut URL-Adressen från din IdP. Den här rutan är ifylld från Azure AD när metadata-URL är löst.
 
*   **X.509-certifikat**: din IdP X.509-certifikat. Den här rutan är ifylld från Azure AD när metadata-URL är löst.
 
*   **Knappen inloggningsnamnet**: namnet på knappen Logga in som organisationen vill att användarna ska se på sidan logga in.
 
*   **SAML användar-ID platser**: den plats där Jira eller växer samman användar-ID förväntades i SAML-svaret. Det kan vara i **NameID** eller i ett namn för anpassat attribut.
 
*   **Attributnamn**: namnet på det attribut som där användar-ID förväntas.
 
*   **Aktivera identifiering av startsfär**: valet att göra om företaget använder Active Directory Federation Services (AD FS) - baserade logga - i.
 
*   **Domännamn**: domännamn om inloggningen är AD FS-baserade.
 
*   **Aktivera enkel utloggning**: markeringen ska göra om du vill logga ut från Azure AD när användaren loggar från Jira eller växer samman.

## <a name="troubleshooting"></a>Felsökning

* **Du håller på att flera certifikatfel**: Logga in på Azure AD och ta bort flera certifikat som är tillgängliga mot appen. Se till att bara ett certifikat finns.

* **Ett certifikat upphör snart att gälla i Azure AD**: tillägg ta hand om automatisk förnyelse av certifikatet. När ett certifikat upphör snart att gälla, ett nytt certifikat ska markeras aktiva och oanvända certifikat ska tas bort. När en användare försöker logga in på Jira i det här scenariot plugin-programmet hämtar och sparar det nya certifikatet.

* **Vill du inaktivera WebSudo (inaktivera säker administratör sessionen)**:
    
  * För Jira aktiverad säker administratör sessioner (det vill säga bekräftade lösenordet innan du använder funktioner för administration) som standard. Om du vill ta bort den här möjligheten i din Jira-instans anger du följande rad i filen jira config.properties: `ira.websudo.is.disabled = true`
    
  * För antal samverkande, Följ stegen på den [växer samman supportwebbplats](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Fält som ska fyllas i metadata-URL komma fylls inte**:
    
  * Kontrollera om URL: en är korrekt. Kontrollera om du har mappat rätt klient- och app-ID.
    
  * Anger en URL i en webbläsare och se om du får XML-federationsmetadata.

* **Det finns ett internt serverfel**: i loggarna i loggkatalogen för installationen. Om du får felet när användaren försöker logga in med hjälp av Azure AD SSO, kan du dela loggarna med supportgruppen.

* **Det finns ett användar-ID inte att hitta ”-fel när användaren försöker logga in**: skapa användar-ID i Jira eller växer samman.

* **Det finns ett ”inte att hitta appen”-fel i Azure AD**: se om rätt URL mappas till appen i Azure AD.

* **Du behöver support**: nå ut till den [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Teamet svarar i 24-48 kontorstid.
    
  Du kan också medföra ett supportärende med Microsoft via Azure portal-kanalen.