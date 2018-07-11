---
title: 'Azure AD Connect: Direktautentisering | Microsoft Docs'
description: Den här artikeln beskrivs Azure Active Directory (Azure AD)-direktautentisering och hur den hjälper Azure AD-inloggningar genom att verifiera användarnas lösenord gentemot en lokal Active Directory.
services: active-directory
keywords: Vad är Azure AD Connect direktautentisering, installera Active Directory, komponenter som krävs för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2d88bf5d20beb9de9bf4a0cdcb43548d0d582779
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917286"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Användaren logga in med Azure Active Directory-direktautentisering

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Vad är Azure Active Directory-direktautentisering?

Med direktautentisering för Azure Active Directory (AD Azure) kan användarna logga in på både lokala och molnbaserade program med samma lösenord. Med ett lösenord mindre att komma ihåg får användarna en bättre upplevelse och IT-kostnaderna blir lägre då det är mindre troligt att användarna glömmer hur inloggningen går till. När användarna loggar in på Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Den här funktionen är ett alternativ till [Lösenordshashsynkronisering för Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), som erbjuder samma fördelen med autentisering i molnet för organisationer. Principer för säkerhet och efterlevnad i vissa organisationer tillåter dock inte dessa organisationer att skicka användarnas lösenord, även i ett hashade formulär utanför deras interna gränserna. Direktautentisering är rätt lösning för dessa organisationer.

![Azure AD-direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Du kan kombinera direktautentisering med den [sömlös enkel inloggning](active-directory-aadconnect-sso.md) funktionen. Det här sättet när användarna har åtkomst till program på sina företagets datorer i företagsnätverket, behöver de inte att skriva in sina lösenord för inloggning.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Viktiga fördelar med att använda Azure AD-direktautentisering

- *Bra användarupplevelse*
  - Användare använda samma lösenord för att logga in på både lokala och molnbaserade program.
  - Användarna snabbare kan prata IT supportavdelningen lösa lösenord-relaterade problem.
  - Användarna kan slutföra [egen lösenordshantering](../authentication/active-directory-passwords-overview.md) uppgifter i molnet.
- *Enkelt att distribuera och administrera*
  - Inget behov av komplicerade lokala distributioner eller nätverkskonfiguration.
  - Behöver en enkel agent är installerade på plats.
  - Inga hanteringskostnader för. Agenten får automatiskt förbättringar och felkorrigeringar.
- *Skydda*
  - Lokala lösenord lagras aldrig i molnet i någon form.
  - Agenten gör bara utgående anslutningar från ditt nätverk. Det finns därför inga krav på att installera agenten i ett perimeternätverk, även kallat DMZ.
  - Skyddar dina användarkonton genom att arbeta smidigt med [principer för Azure AD villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md), inklusive Multi-Factor Authentication (MFA) och av [filtrerar ut lösenordet för råstyrkeattacker](../authentication/howto-password-smart-lockout.md).
- *Med hög tillgänglighet*
  - Ytterligare agenter kan installeras på flera lokala servrar för att ge hög tillgänglighet för inloggningsförfrågningar.

## <a name="feature-highlights"></a>Funktionen visar

- Har stöd för användarinloggning i alla web webbläsarbaserade program och i Microsoft Office-klientprogram som använder [modern autentisering](https://aka.ms/modernauthga).
- Logga in användarnamn kan vara antingen en lokal Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
- Funktionen fungerar sömlöst med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Multi-Factor Authentication (MFA) för att skydda dina användare.
- Integrerad med molnbaserade [egen lösenordshantering](../authentication/active-directory-passwords-overview.md), inklusive tillbakaskrivning till lokala Active Directory och lösenordsskydd av förbjuda vanliga lösenord.
- Miljöer med Multi-Forest stöds om det finns skogsförtroenden mellan AD-skogar och om routning är korrekt konfigurerad.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den kan aktiveras [Azure AD Connect](active-directory-aadconnect.md).
- Den använder en enkel lokal agent som lyssnar efter och svarar på begäranden för verifiering av lösenord.
- Installerar flera agenter ger hög tillgänglighet för inloggningsförfrågningar.
- Den [skyddar](../authentication/howto-password-smart-lockout.md) dina lokala konton mot brute force angrepp för lösenord i molnet.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och köra Azure AD-direktautentisering.
- [**Smart kontoutelåsning** ](../authentication/howto-password-smart-lockout.md) -konfigurera Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Teknisk djupdykning** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -förstår hur den här funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Djupgående om säkerhet** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -mer djupgående teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här tilläggsfunktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.
