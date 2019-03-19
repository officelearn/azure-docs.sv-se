---
title: 'Azure AD Connect: Sömlös enkel inloggning | Microsoft Docs'
description: Det här avsnittet beskriver Azure Active Directory (Azure AD) sömlös enkel inloggning och hur den hjälper dig att ange true enkel inloggning för företagets datoranvändare i företagsnätverket.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c34d8de3dfd06540dd50542ab19da0c1d9b1567
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079749"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory sömlös enkel inloggning

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Vad är Azure Active Directory sömlös enkel inloggning?

Sömlös enkel inloggning med Azure Active Directory (sömlös SSO med Azure AD) loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket. När aktiverad, behöver inte användarna ange sina lösenord och logga in på Azure AD, även skriver i deras användarnamn. Den här funktionen ger användarna enkel åtkomst till dina molnbaserade program utan några ytterligare lokala komponenter.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Sömlös enkel inloggning kan kombineras med antingen den [Lösenordshashsynkronisering](how-to-connect-password-hash-synchronization.md) eller [direktautentisering](how-to-connect-pta.md) inloggning metoder. Sömlös SSO är _inte_ tillämpliga till Active Directory Federation Services (ADFS).

![Sömlös enkel inloggning](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Sömlös enkel inloggning måste användarens enhet ska vara **domänanslutna**, men behöver inte enheten behöver [Azure AD har anslutits](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Viktiga fördelar

- *En utmärkt användarupplevelse*
  - Användare loggas automatiskt till både lokala och molnbaserade program.
  - Användarna behöver inte ange sina lösenord flera gånger.
- *Enkelt att distribuera och administrera*
  - Inga ytterligare komponenter krävs för en lokal plats till att det ska fungera.
  - Fungerar med valfri metod för autentisering i molnet – [Lösenordshashsynkronisering](how-to-connect-password-hash-synchronization.md) eller [direktautentisering](how-to-connect-pta.md).
  - Kan distribueras till vissa eller alla användare med en Grupprincip.
  - Registrera Windows 10-enheter med Azure AD utan behov av alla AD FS-infrastruktur. Den här funktionen kräver att du använder version 2.1 eller senare av den [till arbetsplatsen klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Funktionen visar

- Logga in användarnamnet kan vara antingen en lokal Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (`Alternate ID`). Båda använder fall arbetet eftersom sömlös SSO använder den `securityIdentifier` anspråk i Kerberos-biljetten för att leta upp motsvarande användarobjekt i Azure AD.
- Sömlös SSO är en opportunistisk funktion. Om det misslyckas av någon anledning går inloggningen för användaren tillbaka till dess vanligt beteende - dvs, användaren måste ange sitt lösenord på sidan logga in.
- Om ett program (till exempel `https://myapps.microsoft.com/contoso.com`) vidarebefordrar en `domain_hint` (OpenID Connect) eller `whr` (SAML) parametern – identifiera din klient eller `login_hint` parametern – identifiera den här användaren, användare finns i Azure AD-inloggningen begäran loggas in automatiskt utan dem att ange användarnamn eller lösenord.
- Användare får också en tyst inloggning om ett program (till exempel `https://contoso.sharepoint.com`) skickar inloggningsförfrågningar till Azure AD-slutpunkter som klienter – det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure AD: s vanliga slutpunkt - det vill säga `https://login.microsoftonline.com/common/<...>` .
- Logga ut stöds. På så sätt kan användarna kan välja en annan Azure AD-konto för att logga in med, i stället för att automatiskt som loggat in med sömlös enkel inloggning automatiskt.
- Office 365 Win32-klienter (Outlook, Word, Excel och andra) med versioner 16.0.8730.xxxx och senare stöds med hjälp av en icke-interaktiv flow. För OneDrive, måste du aktivera den [OneDrive tyst config funktionen](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för en tyst inloggningsupplevelse.
- Den kan aktiveras via Azure AD Connect.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den stöds på webbläsarbaserade webbklienter och Office-klienter som har stöd för [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) på plattformar och webbläsare som stöder Kerberos-autentisering:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Nej|Ja|Ja\*\*\*|Gäller inte
|Windows 8.1|Ja\*|Gäller inte|Ja|Ja\*\*\*|Gäller inte
|Windows 8|Ja\*|Gäller inte|Ja|Ja\*\*\*|Gäller inte
|Windows 7|Ja\*|Gäller inte|Ja|Ja\*\*\*|Gäller inte
|Windows Server 2012 R2 or above|Ja\*\*|Gäller inte|Ja|Ja\*\*\*|Gäller inte
|Mac OS X|Gäller inte|Gäller inte|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


\*Kräver Internet Explorer version 10 eller senare

\*\*Kräver Internet Explorer version 10 eller senare. Inaktivera Förbättrad skyddat läge

\*\*\*Kräver [ytterligare konfiguration](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>För Windows 10, rekommendationen är att använda [Azure AD Join](../active-directory-azureadjoin-overview.md) för den optimala enkel inloggning med Azure AD.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](how-to-connect-sso-quick-start.md) – komma igång och köra sömlös enkel inloggning för den Azure AD.
- [**Distributionsplan** ](https://aka.ms/AuthenticationDeploymentPlan) -stegvisa distributionsplan.
- [**Teknisk djupdykning** ](how-to-connect-sso-how-it-works.md) -förstår hur den här funktionen fungerar.
- [**Vanliga frågor och svar** ](how-to-connect-sso-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](tshoot-connect-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.

