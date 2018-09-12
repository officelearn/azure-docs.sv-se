---
title: 'Azure AD Connect: Sömlös enkel inloggning | Microsoft Docs'
description: Det här avsnittet beskriver Azure Active Directory (Azure AD) sömlös enkel inloggning och hur den hjälper dig att ange true enkel inloggning för företagets datoranvändare i företagsnätverket.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bc3b5cfc106fdb90e99fb918113f4f371f928b26
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377963"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory sömlös enkel inloggning

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Vad är Azure Active Directory sömlös enkel inloggning?

Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När aktiverad, behöver inte användarna ange sina lösenord och logga in på Azure AD, även skriver i deras användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Sömlös enkel inloggning kan kombineras med antingen den [Lösenordshashsynkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md) eller [direktautentisering](active-directory-aadconnect-pass-through-authentication.md) inloggning metoder. Sömlös SSO är _inte_ tillämpliga till Active Directory Federation Services (ADFS).

![Sömlös enkel inloggning](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Sömlös enkel inloggning måste användarens enhet ska vara **domänanslutna**, men behöver inte enheten behöver [Azure AD har anslutits](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Viktiga fördelar

- *Bra användarupplevelse*
  - Användare loggas automatiskt till både lokala och molnbaserade program.
  - Användarna behöver inte ange sina lösenord flera gånger.
- *Enkelt att distribuera och administrera*
  - Inga ytterligare komponenter krävs för en lokal plats till att det ska fungera.
  - Fungerar med valfri metod för autentisering i molnet – [Lösenordshashsynkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md) eller [direktautentisering](active-directory-aadconnect-pass-through-authentication.md).
  - Kan distribueras till vissa eller alla användare med en Grupprincip.
  - Registrera Windows 10-enheter med Azure AD utan behov av alla AD FS-infrastruktur. Den här funktionen kräver att du använder version 2.1 eller senare av den [till arbetsplatsen klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funktionen visar

- Logga in användarnamnet kan vara antingen en lokal Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (`Alternate ID`). Båda använder fall arbetet eftersom sömlös SSO använder den `securityIdentifier` anspråk i Kerberos-biljetten för att leta upp motsvarande användarobjekt i Azure AD.
- Sömlös SSO är en opportunistisk funktion. Om det misslyckas av någon anledning går inloggningen för användaren tillbaka till dess vanligt beteende - dvs, användaren måste ange sitt lösenord på sidan logga in.
- Om ett program (till exempel https://myapps.microsoft.com/contoso.com) vidarebefordrar en `domain_hint` (OpenID Connect) eller `whr` (SAML) parametern – identifiera din klient eller `login_hint` parametern – identifiera den här användaren, användare finns i Azure AD-inloggningen begäran loggas in automatiskt utan dem att ange användarnamn eller lösenord.
- Användare får också en tyst inloggning om ett program (till exempel https://contoso.sharepoint.com) skickar inloggningsförfrågningar till Azure AD: s klientslutpunkter – det vill säga https://login.microsoftonline.com/contoso.com/<..> eller https://login.microsoftonline.com/<tenant_ID>/<..> – i stället för Azure AD: s vanliga slutpunkt - det vill säga https://login.microsoftonline.com/common/<...>.
- Logga ut stöds. På så sätt kan användarna kan välja en annan Azure AD-konto för att logga in med, i stället för att automatiskt som loggat in med sömlös enkel inloggning automatiskt.
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versioner 16.0.8730.xxxx och senare stöds med hjälp av en icke-interaktiv flow. För OneDrive, måste du aktivera den [OneDrive tyst config funktionen](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för en tyst inloggningsupplevelse.
- Den kan aktiveras via Azure AD Connect.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den stöds på webbläsarbaserade webbklienter och Office-klienter som har stöd för [modern autentisering](https://aka.ms/modernauthga) på plattformar och webbläsare som stöder Kerberos-autentisering:

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Nej|Ja|Ja\*\*|Ej tillämpligt
|Windows 8.1|Ja\*|Ej tillämpligt|Ja|Ja\*\*|Ej tillämpligt
|Windows 8|Ja\*|Ej tillämpligt|Ja|Ja\*\*|Ej tillämpligt
|Windows 7|Ja\*|Ej tillämpligt|Ja|Ja\*\*|Ej tillämpligt
|Mac OS X|Ej tillämpligt|Ej tillämpligt|Ja\*\*|Ja\*\*|Ja\*

\*Kräver Internet Explorer version 10 eller senare

\*\*Kräver [ytterligare konfiguration](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>För Windows 10, rekommendationen är att använda [Azure AD Join](../active-directory-azureadjoin-overview.md) för den optimala enkel inloggning med Azure AD.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-sso-quick-start.md) – komma igång och köra sömlös enkel inloggning för den Azure AD.
- [**Distributionsplan** ](https://aka.ms/AuthenticationDeploymentPlan) -stegvisa distributionsplan.
- [**Teknisk djupdykning** ](active-directory-aadconnect-sso-how-it-works.md) -förstår hur den här funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-sso-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.

