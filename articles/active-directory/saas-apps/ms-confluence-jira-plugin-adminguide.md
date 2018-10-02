---
title: Atlassians Jira/antal samverkande administratörshandboken - Azure Active Directory | Microsoft Docs
description: Administratörshandboken att använda Atlassians Jira och växer samman med Azure Active Directory (Azure AD)...
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: jeedes
ms.openlocfilehash: 49516523abdd927c3ae60235fcd74473689c6856
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019872"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassians Jira och växer samman administratörshandboken för Azure Active Directory

## <a name="overview"></a>Översikt

Den Azure Active Directory (Azure AD) enkel inloggning (SSO) plugin-programmet Microsoft Azure AD ger kunder möjlighet att använda sitt arbets- eller skolkonto konto för att logga in till Atlassians Jira och växer samman Server-baserade produkter. Den implementerar SAML 2.0-baserad enkel inloggning.

## <a name="how-it-works"></a>Hur det fungerar

När användarna vill logga in i Atlassians Jira eller växer samman program, visas den **logga in med Azure AD** på sidan logga in. När de väljer den, måste de logga in med hjälp av Azure AD organisation på inloggningssidan (det vill säga sina arbets- eller skolkonto-konto).

När användarna har autentiserats kan ska de kunna logga in till programmet. Om de redan har autentiserats med ID och lösenord för sitt arbets- eller skolkonto konto och sedan direkt inloggning till programmet. 

Inloggning fungerar över Jira och växer samman. Om användare är inloggad på Jira-program och antal samverkande öppnas i samma webbläsarfönster som de inte behöver ange autentiseringsuppgifterna för den andra appen. 

Användare får också åtkomst till Atlassians produkten via Mina appar under arbets-eller skolkonto. De bör vara inloggad utan att ange autentiseringsuppgifter.

> [!NOTE]
> Användaretablering görs inte via plugin-programmet.

## <a name="audience"></a>Målgrupp

Jira och antal samverkande administratörer kan använda plugin-programmet för att aktivera enkel inloggning med hjälp av Azure AD.

## <a name="assumptions"></a>Antaganden

* Jira och antal samverkande instanser är HTTPS-aktiverade.
* Användare har redan skapats i Jira eller växer samman.
* Användare har roller som tilldelats i Jira eller växer samman.
* Administratörer har åtkomst till information som krävs för att konfigurera plugin-programmet.
* Jira eller växer samman finns utanför företagets nätverk.
* Plugin-programmet fungerar med endast den lokala versionen av Jira och växer samman.

## <a name="prerequisites"></a>Förutsättningar

Observera följande innan du installerar plugin-programmet:

* Jira och antal samverkande är installerade på en Windows 64-bitarsversion.
* Jira och antal samverkande versioner är HTTPS-aktiverade.
* Jira och antal samverkande finns på internet.
* Administratörsautentiseringsuppgifter används i Jira och växer samman.
* Administratörsautentiseringsuppgifter är på plats för Azure AD.
* WebSudo har inaktiverats i Jira och växer samman.

## <a name="supported-versions-of-jira-and-confluence"></a>Vilka versioner av Jira och växer samman

Plugin-programmet stöder följande versioner av Jira och växer samman:

* Jira Core- och programvara: 6.0 7.8
* Jira-supporten: 3.0 till 3.2
* Antal samverkande: 5.0 till 5.10

## <a name="installation"></a>Installation

Följ dessa steg för att installera plugin-programmet:

1. Logga in på din instans av Jira eller växer samman som en administratör.

2. Gå till Jira/antal samverkande-administrationskonsolen och välj **tillägg**.

3. Från Marketplace Atlassians, Sök efter **Microsoft SAML SSO-pluginprogrammet**.

   Lämplig version av plugin-programmet visas i sökresultaten.

4. Välj plugin-programmet och universella plugin-programmet för Manager (finns på) installerar den.

När plugin-programmet har installerats, visas den i den **användare installerat tillägg** delen av **Hantera tillägg**.

## <a name="plug-in-configuration"></a>Konfiguration av pluginprogrammet

Innan du börjar använda plugin-programmet, måste du konfigurera den. Plugin-programmet väljer den **konfigurera** knappen och ange konfigurationsinformationen.

Följande bild visar konfigurationsskärmen i både Jira och växer samman:

![Plugin-programmet konfigurationsskärmen](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Metadata-URL**: URL: en att hämta federationsmetadata från Azure AD.

*   **Identifierare**: URL: en Azure AD använder för att verifiera källan för begäran. Det mappas till den **identifierare** element i Azure AD. Plugin-programmet automatiskt kommer den här URL: en som https://*< domän: port >*/.

*   **Svars-URL**: svars-URL i din identitetsprovider (IdP) som initierar SAML-inloggningen. Det mappas till den **svars-URL** element i Azure AD. Plugin-programmet automatiskt kommer den här URL: en som https://*< domän: port >*/plugins/servlet/saml/auth.

*   **Inloggning på URL: en**: inloggnings-URL: en i IDP: N som initierar SAML-inloggningen. Det mappas till den **inloggning** element i Azure AD. Plugin-programmet automatiskt kommer den här URL: en som https://*< domän: port >*/plugins/servlet/saml/auth.

*   **IdP entitets-ID**: entitets-ID som IDP: N använder. Den här rutan fylls när metadata-URL har åtgärdats.

*   **Inloggnings-URL**: Logga in URL: en från IDP: N. Den här rutan är ifylld från Azure AD när metadata-URL har åtgärdats.

*   **URL för utloggning**: URL för utloggning från IDP: N. Den här rutan är ifylld från Azure AD när metadata-URL har åtgärdats.

*   **X.509-certifikat**: Your IdP X.509-certifikat. Den här rutan är ifylld från Azure AD när metadata-URL har åtgärdats.

*   **Knappen inloggningsnamn**: namnet på knappen Logga in som organisationen vill att användarna ska se på sidan logga in.

*   **SAML-ID användarplatser**: den plats där användar-ID Jira eller växer samman förväntas i SAML-svar. Det kan vara i **NameID** eller i en anpassade attributets namn.

*   **Attributnamn**: namnet på det attribut som där användar-ID förväntas.

*   **Aktivera identifiering av startsfär**: alternativet att göra om företaget använder Active Directory Federation Services (AD FS) - baserad inloggning - i.

*   **Domännamn**: domännamnet om inloggningen är AD FS-baserade.

*   **Aktivera enkel utloggning**: valet för att göra om du vill logga ut från Azure AD när en användare loggar ut från Jira eller växer samman.

## <a name="troubleshooting"></a>Felsökning

* **Du får flera certifikatfel**: Logga in på Azure AD och ta bort flera certifikat som är tillgängliga mot appen. Kontrollera att bara ett certifikat finns.

* **Ett certifikat som snart upphör att gälla i Azure AD**: tillägg tar hand om automatisk förnyelse av certifikatet. När ett certifikat upphör snart att gälla, ett nytt certifikat ska markeras som aktiv och bör tas bort oanvända certifikat. När en användare försöker logga in på Jira i det här scenariot, plugin-programmet hämtar och sparar det nya certifikatet.

* **Vill du inaktivera WebSudo (inaktivera säker administratör-session)**:

  * För Jira, skydda administratör sessioner (det vill säga Lösenordsbekräftelse innan du använder administrativa funktioner) är aktiverade som standard. Om du vill ta bort den här möjligheten i Jira-instans anger du följande rad i filen jira-config.properties: `ira.websudo.is.disabled = true`

  * För antal samverkande, kan du följa stegen i den [växer samman supportwebbplats](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Fält som ska ske av metadata-URL: en inte komma in**:

  * Kontrollera om URL: en är korrekt. Kontrollera om du har mappat den rätt klient- och app-ID.

  * Ange URL: en i en webbläsare och se om du får federationsmetadata XML.

* **Det finns ett internt serverfel**: granska loggarna i loggkatalogen av installationen. Om du får felet när användaren försöker logga in med enkel inloggning för Azure AD, kan du dela loggarna med supporten.

* **Det finns ett ”användar-ID hittades inte”-fel när användaren försöker att logga in**: skapa användar-ID i Jira eller växer samman.

* **Det finns ett ”appen kan inte hittas”-fel i Azure AD**: se om lämplig Webbadress är mappad till appen i Azure AD.

* **Du behöver support**: Kontakta den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Teamet svarar i 24 – 48 kontorstid.

  Du kan också medföra ett supportärende hos Microsoft via Azure portal-kanalen.

## <a name="plug-in-faq"></a>Plugin-programmet vanliga frågor och svar

Finns under vanliga frågor och svar om du har en fråga angående den här plugin-programmet.

### <a name="what-does-the-plug-in-do"></a>Vad gör i plugin-programmet?

Plugin-programmet tillhandahåller enkel inloggning (SSO)-funktionen för Atlassians Jira (inklusive Jira Core, Jira Software, Jira Service Desk) och växer samman lokal programvara. Plugin-programmet fungerar med Azure Active Directory (Azure AD) som en identitetsleverantör (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Vilka produkter Atlassians fungerar plugin-programmet med?

Plugin-programmet fungerar med lokala versioner av Jira och växer samman.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Fungerar plugin-programmet i molnet versioner?

Nej. Plugin-programmet stöder endast lokala versioner av Jira och växer samman.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Vilka versioner av Jira och växer samman stöder plugin-programmet?

Plugin-programmet stöder följande versioner:

* Jira Core- och programvara: 6.0 7.8
* Jira-supporten: 3.0 till 3.2
* Antal samverkande: 5.0 till 5.10

### <a name="is-the-plug-in-free-or-paid"></a>Är plugin-programmet kostnadsfri eller betald?

Det är ett kostnadsfritt tillägg.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Jag behöver starta om Jira eller växer samman när jag har distribuerat plugin-programmet?

En omstart krävs inte. Du kan börja använda plugin-programmet direkt.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hur får jag support för plugin-programmet?

Du kan nå ut till den [enkel inloggning för Azure AD-integrering Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) för support krävs för det här plugin-programmet. Teamet svarar i 24 – 48 kontorstid.

Du kan också medföra ett supportärende hos Microsoft via Azure portal-kanalen.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Skulle plugin-programmet arbetet på en Mac- eller Ubuntu installation av Jira och växer samman?

Vi har testat plugin-programmet bara på 64-bitars Windows Server-installationer av Jira och växer samman.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Fungerar plugin-programmet med IDP: er än Azure AD?

Nej. Den fungerar bara med Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Vilken version av SAML fungerar plugin-programmet med?

Det fungerar med SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Plugin-programmet gör etableringen av användare?

Nej. Plugin-programmet innehåller endast SAML 2.0-baserad enkel inloggning. Användaren måste tillhandahållas i programmet innan inloggning SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Har stöd för plugin-programmet kluster-versioner av Jira och växer samman?

Nej. Plugin-programmet fungerar med lokala versioner av Jira och växer samman.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Fungerar plugin-programmet med HTTP-versioner av Jira och växer samman?

Nej. Plugin-programmet fungerar med endast HTTPS-aktiverad installationer.