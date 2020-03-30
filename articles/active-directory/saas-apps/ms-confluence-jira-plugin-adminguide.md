---
title: Administratörsguide för Atlassian Jira/Sammanflödet – Azure Active Directory| Microsoft-dokument
description: Administratörsguide för att använda Atlassian Jira och Sammanflödet med Azure Active Directory (Azure AD)..
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161207"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Administratörsguide för Atlassian Jira och Sammanflödet för Azure Active Directory

## <a name="overview"></a>Översikt

Azure Active Directory (Azure AD) plugin-program (Single Sign-on) gör det möjligt för Microsoft Azure AD-kunder att använda sitt arbets- eller skolkonto för att logga in på Atlassian Jira- och Confluence Server-baserade produkter. Den implementerar SAML 2.0-baserade SSO.

## <a name="how-it-works"></a>Hur det fungerar

När användare vill logga in på Atlassian Jira eller Confluence-programmet ser de knappen **Logga in med Azure AD** på inloggningssidan. När de väljer det måste de logga in med hjälp av inloggningssidan för Azure AD-organisationen (det vill än deras arbets- eller skolkonto).

När användarna har autentiserats ska de kunna logga in på programmet. Om de redan är autentiserade med ID och lösenord för sitt arbets- eller skolkonto loggar de direkt in på programmet. 

Inloggning fungerar över Jira och Confluence. Om användare är inloggade på Jira-programmet och Sammanflödet öppnas i samma webbläsarfönster behöver de inte ange autentiseringsuppgifterna för den andra appen. 

Användare kan också komma åt Atlassian-produkten via Mina appar under arbets- eller skolkontot. De bör loggas in utan att bli ombedda att ange referenser.

> [!NOTE]
> Användarenablering görs inte via plugin-programmet.

## <a name="audience"></a>Målgrupp

Jira- och sammanflödet kan använda plugin-programmet för att aktivera SSO med hjälp av Azure AD.

## <a name="assumptions"></a>Antaganden

* Jira- och Sammanflödet är HTTPS-aktiverade.
* Användare har redan skapats i Jira eller Sammanflödet.
* Användare har roller som tilldelats i Jira eller Sammanflödet.
* Administratörer har tillgång till information som krävs för att konfigurera plugin-programmet.
* Jira eller Confluence finns även utanför företagets nätverk.
* Plugin-programmet fungerar endast med den lokala versionen av Jira och Confluence.

## <a name="prerequisites"></a>Krav

Observera följande information innan du installerar plugin-programmet:

* Jira och Confluence installeras på en Windows 64-bitarsversion.
* Jira- och Sammanflödets versioner är HTTPS-aktiverade.
* Jira och Confluence finns på internet.
* Administratörsautentiseringsuppgifter finns för Jira och Sammanflödet.
* Administratörsautentiseringsuppgifter finns för Azure AD.
* WebSudo är inaktiverat i Jira och Sammanflödet.

## <a name="supported-versions-of-jira-and-confluence"></a>Versioner av Jira och Sammanflödet som stöds

Plugin-programmet stöder följande versioner av Jira och Sammanflödet:

* Jira Kärna och programvara: 6,0 till 7,12
* Jira Service Desk: 3.0.0 till 3.5.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)
* Sammanflödet: 5,0 till 5,10
* Sammanflödet: 6.0.1
* Sammanflödet: 6.1.1
* Sammanflödet: 6.2.1
* Sammanflödet: 6.3.4
* Sammanflödet: 6.4.0
* Sammanflödet: 6.5.0
* Sammanflödet: 6.6.2
* Sammanflödet: 6.7.0
* Sammanflödet: 6.8.1
* Sammanflödet: 6.9.0
* Sammanflödet: 6.10.0
* Sammanflödet: 6.11.0
* Sammanflödet: 6.12.0

## <a name="installation"></a>Installation

Så här installerar du plugin-programmet:

1. Logga in på jira- eller sammanflödet som administratör.

2. Gå till Jira/Confluence **administrationskonsolen**och välj Tillägg .

3. Från Microsoft Download Center, ladda ner [Microsoft SAML SSO Plugin för Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [Microsoft SAML SSO Plugin för sammanflödet](https://www.microsoft.com/download/details.aspx?id=56503).

   Lämplig version av plugin-programmet visas i sökresultaten.

4. Välj plugin-programmet så installerar UPM (Universal Plug-in Manager) det.

När plugin-programmet har installerats visas det i avsnittet **Användarinstallerade tillägg** i **Hantera tillägg**.

## <a name="plug-in-configuration"></a>Konfiguration av plugin-program

Innan du börjar använda plugin-programmet måste du konfigurera det. Välj plugin-programmet, välj knappen **Konfigurera** och ange konfigurationsinformation.

Följande bild visar konfigurationsskärmen i både Jira och Sammanflödet:

![Konfigurationsskärm för plugin-program](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Metadata-URL:** URL:en för att hämta federationsmetadata från Azure AD.

* **Identifierare**: URL:en som Azure AD använder för att validera källan till begäran. Det mappar till **identifierarelementet** i Azure AD. Plugin-programmet hämtar automatiskt den här URL:en som https://*\<domän:port>*/.

* **Svars-URL:** Svarsadressen i din identitetsleverantör (IdP) som initierar SAML-inloggningen. Det mappas till **svars-URL-elementet** i Azure AD. Plugin-programmet härleder automatiskt den här webbadressen som https://*\<domän:port>*/plugins/servlet/saml/auth.

* **Inloggnings-URL:** Inloggnings-URL:en i IdP som initierar SAML-inloggningen. Den mappar till **inloggningselementet** i Azure AD. Plugin-programmet härleder automatiskt den här webbadressen som https://*\<domän:port>*/plugins/servlet/saml/auth.

* **IdP Entitets-ID**: Entitets-ID som idP använder. Den här rutan fylls i när metadata-URL:en matchas.

* **Inloggningsadress:** Inloggningsadressen från din IdP. Den här rutan fylls i från Azure AD när metadata-URL:en har lösts.

* **Utloggning URL:** Utloggning URL från din IdP. Den här rutan fylls i från Azure AD när metadata-URL:en har lösts.

* **X.509 Certifikat:** Din IdP: s X.509 certifikat. Den här rutan fylls i från Azure AD när metadata-URL:en har lösts.

* **Namn på inloggningsknapp:** Namnet på inloggningsknappen som din organisation vill att användarna ska se på inloggningssidan.

* **SAML-användar-ID-platser:** Den plats där användar-ID:t Jira eller Sammanflödet förväntas i SAML-svaret. Det kan vara i **NameID** eller i ett anpassat attributnamn.

* **Attributnamn:** Namnet på attributet där användar-ID förväntas.

* **Aktivera Home Realm Discovery**: Valet att göra om företaget använder AD FS-baserade inloggning (Active Directory Federation Services).

* **Domännamn:** Domännamnet om inloggning är AD FS-baserad.

* **Aktivera enkel signering:** Valet att göra om du vill logga ut från Azure AD när en användare loggar ut från Jira eller Sammanflödet.

## <a name="troubleshooting"></a>Felsökning

* **Du får flera certifikatfel:** Logga in på Azure AD och ta bort flera certifikat som är tillgängliga mot appen. Kontrollera att det bara finns ett certifikat.

* **Ett certifikat håller på att upphöra att gälla i Azure AD:** Tillägg tar hand om automatisk överrullning av certifikatet. När ett certifikat håller på att upphöra att gälla ska ett nytt certifikat markeras aktivt och oanvända certifikat ska tas bort. När en användare försöker logga in på Jira i det här fallet hämtar och sparar plugin-programmet det nya certifikatet.

* **Du vill inaktivera WebSudo (inaktivera den säkra administratörssessionen):**

  * För Jira aktiveras säkra administratörssessioner (det vill ha lösenordsbekräftelse innan du använder administrationsfunktioner) som standard. Om du vill ta bort den här möjligheten i Jira-instansen anger du följande rad i filen jira-config.properties:`ira.websudo.is.disabled = true`

  * För sammanflödet följer du stegen på [sammanflödets supportwebbplats](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Fält som ska fyllas i av metadata-URL:en fylls inte i:**

  * Kontrollera om webbadressen är korrekt. Kontrollera om du har mappat rätt klient- och app-ID.

  * Ange URL:en i en webbläsare och se om du får xml-koden för federationsmetadata.

* **Det finns ett internt serverfel:** Granska loggarna i loggkatalogen för installationen. Om du får felet när användaren försöker logga in med Hjälp av Azure AD SSO kan du dela loggarna med supportteamet.

* **Det finns ett felmeddelande om att användar-ID inte hittades när användaren försöker logga in:** Skapa användar-ID i Jira eller Sammanflödet.

* **Det finns ett "App hittades inte" i Azure AD:** Se om lämplig URL mappas till appen i Azure AD.

* **Du behöver support:** Nå ut till [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Teamet svarar i 24-48 kontorstid.

  Du kan också höja en supportbiljett med Microsoft via Azure-portalkanalen.

## <a name="plug-in-faq"></a>Vanliga frågor om plugin-program

Se vanliga frågor nedan om du har några frågor om detta plugin-program.

### <a name="what-does-the-plug-in-do"></a>Vad gör plugin-programmet?

Plugin-programmet ger SSO-kapacitet (Single Sign-on) för Atlassian Jira (inklusive Jira Core, Jira Software, Jira Service Desk) och Confluence on-premises software. Plugin-programmet fungerar med Azure Active Directory (Azure AD) som identitetsprovider (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Vilka Atlassian-produkter fungerar plugin-programmet med?

Plugin-programmet fungerar med lokala versioner av Jira och Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Fungerar plugin-programmet på molnversioner?

Nej. Plugin-programmet stöder endast lokala versioner av Jira och Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Vilka versioner av Jira och Sammanflödet stöder plugin-programmet?

Plugin-programmet stöder dessa versioner:

* Jira Kärna och programvara: 6,0 till 7,12
* Jira Service Desk: 3.0.0 till 3.5.0
* JIRA stöder också 5.2. Om du vill ha mer information klickar du på [Microsoft Azure Active Directory single sign-on for JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial) (Microsoft Azure Active Directory enkel inloggning för JIRA 5.2)
* Sammanflödet: 5,0 till 5,10
* Sammanflödet: 6.0.1
* Sammanflödet: 6.1.1
* Sammanflödet: 6.2.1
* Sammanflödet: 6.3.4
* Sammanflödet: 6.4.0
* Sammanflödet: 6.5.0
* Sammanflödet: 6.6.2
* Sammanflödet: 6.7.0
* Sammanflödet: 6.8.1
* Sammanflödet: 6.9.0
* Sammanflödet: 6.10.0
* Sammanflödet: 6.11.0
* Sammanflödet: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Är plug-in gratis eller betalas?

Det är ett gratis tillägg.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Måste jag starta om Jira eller Confluence efter att jag har distribuerat plugin-programmet?

Ingen omstart krävs. Du kan börja använda plugin-programmet omedelbart.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Hur får jag stöd för plugin-programmet?

Du kan kontakta [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) för all support som behövs för det här plugin-programmet. Teamet svarar i 24-48 kontorstid.

Du kan också höja en supportbiljett med Microsoft via Azure-portalkanalen.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Skulle plug-in arbete på en Mac eller Ubuntu installation av Jira och Confluence?

Vi har testat plugin-programmet endast på 64-bitars Windows Server-installationer av Jira och Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Fungerar plugin-programmet med andra IDP än Azure AD?

Nej. Det fungerar bara med Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Vilken version av SAML fungerar plugin-programmet med?

Det fungerar med SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Etablerar plugin-programmet för plugin-program?

Nej. Plugin-programmet innehåller endast SAML 2.0-baserade SSO. Användaren måste etableras i programmet innan SSO-inloggningen.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Stöder plugin-programmet klusterversioner av Jira och Sammanflödet?

Nej. Plugin-programmet fungerar med lokala versioner av Jira och Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Fungerar plugin-programmet med HTTP-versioner av Jira och Sammanflödet?

Nej. Plugin-programmet fungerar endast med HTTPS-aktiverade installationer.
