---
title: 'Azure AD Connect: Sömlös enkel inloggning | Microsoft-dokument'
description: I det här avsnittet beskrivs Azure Active Directory (Azure AD) Seamless Single Sign-On och hur du kan tillhandahålla äkta enkel inloggning för företagsskrivbordsanvändare i företagets nätverk.
services: active-directory
keywords: vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483762"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Sömlös enkel inloggning i Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Vad är Azure Active Directory Seamless Single Sign-On?

Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När det är aktiverat behöver användarna inte skriva in sina lösenord för att logga in på Azure AD, och oftast till och med skriva in sina användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Sömlös SSO kan kombineras med antingen [inloggningsmetoderna Lösenord hash](how-to-connect-password-hash-synchronization.md) eller [direktautentisering.](how-to-connect-pta.md) Sömlös SSO är _inte_ tillämpligt på Active Directory Federation Services (ADFS).

![Sömlös enkel inloggning](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Sömlös SSO behöver användarens enhet för att **domän-anslutas** endast, men det används inte på [Azure AD-anslutna](../devices/concept-azure-ad-join.md) eller [Hybrid Azure AD-anslutna](../devices/concept-azure-ad-join-hybrid.md) enheter. SSO på Azure AD gick med och Hybrid Azure AD gick med i arbeten baserat på den [primära uppdateringstoken](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Viktiga fördelar

- *En utmärkt användarupplevelse*
  - Användare loggas automatiskt in i både lokala och molnbaserade program.
  - Användarna behöver inte ange sina lösenord upprepade gånger.
- *Lätt att distribuera & administrera*
  - Inga ytterligare komponenter behövs lokalt för att få detta att fungera.
  - Fungerar med alla metoder för molnautentisering - Synkronisering av [lösenord hash](how-to-connect-password-hash-synchronization.md) eller [direktautentisering](how-to-connect-pta.md).
  - Kan distribueras till vissa eller alla användare med grupprincip.
  - Registrera enheter som inte är Windows 10-enheter med Azure AD utan att behöva ad FS-infrastruktur. Den här funktionen måste du använda version 2.1 eller senare av [workplace-join-klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Höjdpunkter i funktionen

- Inloggningsanvändarnamnet kan vara antingen det lokala`userPrincipalName`standardanvändarnamnet ( ) eller`Alternate ID`ett annat attribut som konfigurerats i Azure AD Connect ( ). Båda användningsfallen fungerar eftersom `securityIdentifier` Seamless SSO använder anspråket i Kerberos-biljetten för att slå upp motsvarande användarobjekt i Azure AD.
- Seamless SSO är en opportunistisk funktion. Om det misslyckas av någon anledning går användarens inloggningsupplevelse tillbaka till sitt vanliga beteende - dvs användaren måste ange sitt lösenord på inloggningssidan.
- Om ett program `https://myapps.microsoft.com/contoso.com`(till exempel) `domain_hint` vidarebefordrar en `whr` (OpenID Connect) eller (SAML) parameter - identifiera din klient, eller `login_hint` parameter - identifiera användaren, i sin Azure AD inloggningsbegäran, användare automatiskt logga in utan att de anger användarnamn eller lösenord.
- Användare får också en tyst inloggningsupplevelse om `https://contoso.sharepoint.com`ett program (till exempel) skickar inloggningsbegäranden till Azure AD:s slutpunkter som ställts in som klienter - det vill `https://login.microsoftonline.com/contoso.com/<..>` vara eller `https://login.microsoftonline.com/<tenant_ID>/<..>` - i stället för Azure AD:s gemensamma slutpunkt - det vill än `https://login.microsoftonline.com/common/<...>`.
- Ut signera stöds. På så sätt kan användare välja ett annat Azure AD-konto att logga in med, i stället för att automatiskt loggas in med Seamless SSO automatiskt.
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versionerna 16.0.8730.xxxx och högre stöds med hjälp av ett icke-interaktivt flöde. För OneDrive måste du aktivera [OneDrives tysta konfigurationsfunktion](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för en tyst inloggningsupplevelse.
- Den kan aktiveras via Azure AD Connect.
- Det är en kostnadsfri funktion och du behöver inga betalda utgåvor av Azure AD för att kunna använda den.
- Det stöds på webbläsarbaserade klienter och Office-klienter som stöder [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) på plattformar och webbläsare som kan Kerberos-autentisering:

| OS\Webbläsare |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Ja|Ja|Ja\*\*\*|Ej tillämpligt
|Windows 8.1|Ja\*|Ej tillämpligt|Ja|Ja\*\*\*|Ej tillämpligt
|Windows 8|Ja\*|Ej tillämpligt|Ja|Ja\*\*\*|Ej tillämpligt
|Windows 7|Ja\*|Ej tillämpligt|Ja|Ja\*\*\*|Ej tillämpligt
|Windows Server 2012 R2 eller senare|Ja\*\*|Ej tillämpligt|Ja|Ja\*\*\*|Ej tillämpligt
|Mac OS X|Ej tillämpligt|Ej tillämpligt|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


\*Kräver Internet Explorer-versioner 10 eller senare

\*\*Kräver Internet Explorer version 10 eller senare. Inaktivera utökat skyddat läge

\*\*\*Kräver [ytterligare konfiguration](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>För Windows 10 är rekommendationen att använda [Azure AD Join](../devices/concept-azure-ad-join.md) för den optimala enskilda inloggningsupplevelsen med Azure AD.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD Seamless SSO.
- [**Distributionsplan**](https://aka.ms/deploymentplans/sso) - Steg-för-steg-distributionsplan.
- [**Teknisk djupdykning**](how-to-connect-sso-how-it-works.md) - Förstå hur den här funktionen fungerar.
- [**Vanliga frågor**](how-to-connect-sso-faq.md) och svar - Svar på vanliga frågor.
- [**Felsöka**](tshoot-connect-sso.md) - Lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - För att lämna in nya funktionsförfrågningar.

