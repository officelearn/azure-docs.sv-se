---
title: 'Azure AD Connect: Direkt-autentisering | Microsoft Docs'
description: Den här artikeln beskriver Azure Active Directory (AD Azure) direkt-autentisering och hur det kan Azure AD inloggningar genom att verifiera användarnas lösenord mot lokala Active Directory.
services: active-directory
keywords: Vad är Azure AD Connect direkt autentisering, installera Active Directory, komponenter som krävs för Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 377a8255961a4c7fa55f8bbc5cb98ae4deff3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Användaren logga in med Azure Active Directory direkt-autentisering

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Vad är Azure Active Directory direkt-autentisering?

Med direktautentisering för Azure Active Directory (AD Azure) kan användarna logga in på både lokala och molnbaserade program med samma lösenord. Med ett lösenord mindre att komma ihåg får användarna en bättre upplevelse och IT-kostnaderna blir lägre då det är mindre troligt att användarna glömmer hur inloggningen går till. När användarna loggar in på Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Den här funktionen är ett alternativ till [Azure AD-lösenord hash-synkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md), vilket ger samma fördelen med autentisering i molnet och organisationer. Principer för säkerhet och efterlevnad i vissa organisationer tillåter dock inte dessa organisationer att skicka användarnas lösenord, även i en hashformaterats formuläret utanför deras interna gränser. Direkt-autentisering är den rätta lösningen för dessa organisationer.

![Azure AD-direkt-autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Du kan kombinera direkt-autentisering med den [sömlös enkel inloggning](active-directory-aadconnect-sso.md) funktion. Det här sättet när användarna har åtkomst till program på sina företagets datorer i företagsnätverket, de behöver inte ange sina lösenord för inloggning.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Viktiga fördelar med att använda Azure AD direkt-autentisering

- *Bättre användarupplevelse*
  - Användare använda samma lösenord för att logga in på både lokala och molnbaserade program.
  - Användarna snabbare tala med IT supportavdelningen lösa lösenord-relaterade problem.
  - Användarna kan utföra [självbetjäning lösenordshantering](../authentication/active-directory-passwords-overview.md) uppgifter i molnet.
- *Enkelt att distribuera och administrera*
  - Inget behov av komplexa lokala distributioner eller nätverkskonfigurationen.
  - Måste en förenklad agent vara installerad lokalt.
  - Inga hanteringskostnader. Agenten får automatiskt förbättringar och felkorrigeringar.
- *Skydda*
  - Lokala lösenord lagras aldrig i molnet i någon form.
  - Agenten är endast utgående anslutningar från i nätverket. Det finns därför inget krav att installera agenten i ett perimeternätverk, också kallas en demilitariserad zon.
  - Skyddar dina användarkonton med fungerar sömlöst med [Azure AD villkorliga åtkomstprinciper](../active-directory-conditional-access-azure-portal.md), inklusive Multi-Factor Authentication (MFA) och av [filtrera lösenord nyckelsökningsangrepp](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Hög tillgänglighet*
  - Ytterligare agenter kan installeras på flera lokala servrar för att tillhandahålla hög tillgänglighet för inloggningsförfrågningar.

## <a name="feature-highlights"></a>Funktionen visar

- Har stöd för användarinloggning i alla webbläsarbaserad webbprogram och i Microsoft Office-program som använder [modern autentisering](https://aka.ms/modernauthga).
- Logga in användarnamn kan vara antingen lokalt Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
- Funktionen fungerar sömlöst med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Multi-Factor Authentication (MFA) för att skydda dina användare.
- Integrerad med molnbaserad [självbetjäning lösenordshantering](../authentication/active-directory-passwords-overview.md), inklusive tillbakaskrivning av lösenord till lokala Active Directory och lösenordsskydd av förbjuda vanliga lösenord.
- Miljöer med flera skogar stöds om det finns skogsförtroenden mellan AD-skogar och om routning är korrekt konfigurerad.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den kan aktiveras [Azure AD Connect](active-directory-aadconnect.md).
- Den använder en förenklad lokal agent som lyssnar efter och svarar på begäran för verifiering av lösenord.
- Installera flera agenter ger hög tillgänglighet för inloggningsförfrågningar.
- Den [skyddar](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) dina lokala konton mot brute force angrepp av lösenord i molnet.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och körs direkt i Azure AD-autentisering.
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Tekniska ingående** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -förstå hur funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -ytterligare djupt teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
