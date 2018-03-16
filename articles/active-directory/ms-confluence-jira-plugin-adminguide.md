---
title: Microsoft Azure Active Directory enkel inloggning Plugin Admin guiden | Microsoft Docs
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Microsoft Azure Active Directory enkel inloggning för JIRA."
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
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory enkel inloggning Plugin Admin Guide

## <a name="table-of-contents"></a>Innehållsförteckning

1. **[OVERVIEW](#overview)**
2. **[SÅ HÄR FUNGERAR DET](#how-it-works)**
3. **[AUDIENCE](#audience)**
4. **[ANTAGANDEN](#assumptions)**
5. **[NÖDVÄNDIGA KOMPONENTER](#prerequisites)**
6. **[VERSIONER SOM STÖDS AV JIRA OCH VÄXER SAMMAN](#supported-versions-of-jira-and-confluence)**
7. **[INSTALLATION](#installation)**
8. **[PLUGIN-KONFIGURATION](#plugin-configuration)**
9. **[FÄLTET FÖRKLARING FÖR SKÄRM FÖR KONFIGURATION AV TILLÄGG:](#field-explanation-for-add---on-configuration-screen:)**
10. **[FELSÖKNING](#troubleshooting)**

## <a name="overview"></a>Översikt

Dessa tillägg aktivera Microsoft Azure AD kunderna att använda organisation användarnamn och lösenord för inloggning till Atlassian Jira och antal samverkande Server baserade produkter. Den implementerar SAML 2.0 SSO baserat.

## <a name="how-it-works"></a>Hur det fungerar

När du vill logga in på Atlassian Jira eller växer samman de finns på **inloggning med Azure AD** knappen på inloggningssidan. När de klickar på den krävs de för att logga in med Azure AD-organisation inloggningssidan.

När användarna autentiseras ska de kunna logga in på programmet. Om de redan har autentiserats med organisations-ID och lösenord, sedan de direkt logga in i programmet. Observera också att inloggningen fungerar JIRA och växer samman. Om användare är inloggade i JIRA program och antal samverkande visas också i samma webbläsarfönster, de behöver för att logga in en gång och inte behöver ange autentiseringsuppgifterna igen för andra program. Användarna kan också öppna Atlassian-produkten via myapps under Azure-konto och de ska loggas utan att ange autentiseringsuppgifterna.

> [!NOTE]
> Användaretablering inte görs med hjälp av det här tilläggsprogrammet.

## <a name="audience"></a>Målgrupp

JIRA och antal samverkande administratörer som planerar att använda den här plugin-programmet för att aktivera enkel inloggning med Azure AD.

## <a name="assumptions"></a>Antaganden

* JIRA/antal samverkande instansen är HTTPS-aktiverade
* Användare har redan skapats i JIRA/växer samman
* Användare har tilldelats i JIRA/antal samverkande rollen
* Administratörer har åtkomst till information som krävs för att konfigurera plugin-programmet
* JIRA/antal samverkande finns utanför företagets nätverk
* Lägg till om fungerar med endast lokala versionen av JIRA och växer samman

## <a name="prerequisites"></a>Förutsättningar

Observera följande krav innan du kan fortsätta med installationen av tillägget:

* JIRA/antal samverkande är installerade på Windows 64-bitarsversionen
* JIRA/antal samverkande versioner är HTTPS-aktiverade
* Observera versionen som stöds för plugin-programmet i ”versioner som stöds” nedan.
* JIRA/antal samverkande är tillgänglig på internet.
* Administratörsautentiseringsuppgifter för JIRA/växer samman
* Autentiseringsuppgifter som administratör för Azure AD
* WebSudo bör inaktiveras i JIRA och växer samman

## <a name="supported-versions-of-jira-and-confluence"></a>Versioner som stöds av JIRA och växer samman

Från och med nu stöds följande versioner av JIRA och växer samman:

* JIRA Core- och programvara: 6.0 7.2.0
* JIRA helpdesk: 3.0 och 3.2
* Antal samverkande: 5.0 till 5.10

## <a name="installation"></a>Installation

Admin bör följa stegen som anges nedan för att installera plugin-programmet:

1. Logga in på din JIRA/antal samverkande-instans som administratör
    
2. Gå till Administration av JIRA/växer samman och klicka på tillägg.
    
3. Söka efter från Atlassian marknadsplatsen **Microsoft SAML SSO Plugin**
 
4. Lämplig version av tillägget visas i sökningen
 
5. Välj plugin-programmet och installerar finns på samma.
 
6. När plugin-programmet installeras, visas det i användaren installerat tillägg i avsnittet Hantera tillägg
 
7. Du måste konfigurera plugin-programmet innan du börjar använda den.
 
8. Klicka på plugin-programmet och konfigurera knappen.
 
9. Klicka på den för att tillhandahålla konfiguration indata
    
## <a name="plugin-configuration"></a>Plugin-konfiguration

Följande bild visar skärm för konfiguration av tillägg i både JIRA och växer samman
    
![tillägget konfiguration](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Fältet förklaring för skärm för konfiguration av tillägg:

1.   URL för tjänstmetadata: URL: en att hämta federationsmetadata från Azure AD
 
2.   ID: Används av Azure AD för att validera källan för begäran. Detta mappas till ID-elementet i Azure AD. Det här är automatisk härleds genom att plugin-programmet som https://<domain:port>/
 
3.   Svars-URL: Använd Reply URL i din IdP att initiera SAML-inloggningen. Det här mappar i elementet Reply URL i Azure AD. Det här är automatisk härleds genom att plugin-programmet som https://<domain:port>/plugins/servlet/saml/auth
 
4.   URL: en inloggning: Använd logga på URL: en i din IdP att initiera SAML-inloggningen. Det här mappar i elementet inloggning i Azure AD. Det här är automatisk härleds genom att plugin-programmet som https://<domain:port>/plugins/servlet/saml/auth
 
5.   IdP enhets-ID: Den enhets-ID som din IdP använder. Detta fylls när Metadata-URL är löst.
 
6.   Inloggnings-URL: Inloggnings-URL från din IdP. Detta är ifylld från Azure AD när Metadata-URL är löst.
 
7.   URL för utloggning: Logga ut URL: en från din IdP. Detta är ifylld från Azure AD när Metadata-URL är löst.
 
8.   X.509-certifikat: Din IdP X.509-certifikat. Detta är ifylld från Azure AD när Metadata-URL är löst.
 
9.   Knappen inloggningsnamn: Name inloggningsknappen organisationen vill se. Den här texten visas för användarna på inloggningsknappen på inloggningsskärmen.
 
10.   Platser för SAML användar-ID: Där användar-id förväntades i SAML-svar. Det kan antingen vara i NameID eller i ett namn för anpassat attribut. Detta ID måste vara JIRA/antal samverkande användar-id.
 
11.   Attributnamn: namnet på det attribut som där användar-Id förväntades.
 
12.   Startsida för aktivera identifiering av startsfär: Kontrollera den här flaggan om företaget genom att använda AD FS-baserade inloggningen.
 
13.   Domännamn: Ange domännamnet här vid inloggningen ADFS-baserade
 
14.   Aktivera enkel inloggning ut: Kontrollera den här office om du vill logga ut från Azure AD när en användare loggar från JIRA/växer samman.

### <a name="troubleshooting"></a>Felsökning

* Om du får felmeddelanden för flera certifikat
    
    * Logga in på Azure AD och ta bort flera certifikat som är tillgängliga mot appen. Kontrollera att det finns bara ett certifikat finns.

* Certifikatet upphör snart att gälla i Azure AD.
    
    * Tillägg hand ta om automatisk förnyelse av certifikatet. När ett certifikat ska gälla nya certifikatet ska markeras active och används inte certifikatet bör tas bort. När en användare försöker logga in på JIRA i det här scenariot tillägg hämtar det nya certifikatet och spara i plugin-programmet.

* Så här inaktiverar du WebSudo (inaktivera säker administratör session)
    
    * JIRA: Skydda administratör sessioner (det vill säga bekräftade lösenordet innan du använder funktioner för administration) är aktiverat som standard. Om du vill inaktivera det i din JIRA-instans kan du inaktivera den här funktionen genom att ange följande rad i filen jira config.properties ”: ira.websudo.is.disabled = true”
    
    * Antal samverkande: Följ stegen som anges i följande URL https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Fält som ska fyllas i Metadata-URL komma fylls inte
    
    * Kontrollera om URL: en är korrekt. Kontrollera om du har mappat rätt klient och app-id.
    
    * Klicka på Webbadressen i webbläsaren och se om du tar emot XML-federationsmetadata.

* Internt serverfel:
    
    * Gå igenom loggarna som finns i loggkatalogen för installationen. Om du får felet när användaren försöker logga in med Azure AD SSO, kan du dela loggarna med supportinformation som anges nedan i det här dokumentet.

* Användar-ID hittades inte när användaren försöker logga in
    
    * Användaren har inte skapats i JIRA/växer samman, så du måste skapa samma.

* Gick inte att hitta i Azure AD App
    
    * Se om rätt URL mappas till appen i Azure AD.

* Information om stöd: nå ut till oss på: [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Vi svarar inom 24-48 kontorstid.
    
    * Du kan också medföra ett supportärende med Microsoft via Azure portal-kanalen.