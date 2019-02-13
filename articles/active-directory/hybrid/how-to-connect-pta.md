---
title: 'Azure AD Connect: Direktautentisering | Microsoft Docs'
description: Den här artikeln beskrivs Azure Active Directory (Azure AD)-direktautentisering och hur den hjälper Azure AD-inloggningar genom att verifiera användarnas lösenord gentemot en lokal Active Directory.
services: active-directory
keywords: Vad är Azure AD Connect direktautentisering, installera Active Directory, komponenter som krävs för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e84324456aef12070cf9355fb17e132f9f99b80
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202799"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Användaren logga in med Azure Active Directory-direktautentisering

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Vad är Azure Active Directory-direktautentisering?

Med direktautentisering för Azure Active Directory (AD Azure) kan användarna logga in på både lokala och molnbaserade program med samma lösenord. Med ett lösenord mindre att komma ihåg får användarna en bättre upplevelse och IT-kostnaderna blir lägre då det är mindre troligt att användarna glömmer hur inloggningen går till. När användarna loggar in på Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Den här funktionen är ett alternativ till [Lösenordshashsynkronisering för Azure AD](how-to-connect-password-hash-synchronization.md), som erbjuder samma fördelen med autentisering i molnet för organisationer. Vissa organisationer som vill genomdriva deras lokala Active Directory säkerhets- och lösenordsprinciper, kan dock välja att använda direktautentisering i stället. Granska [den här guiden](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) en jämförelse av de olika Azure AD-inloggning metoderna och hur du väljer den direkt inloggningsmetoden för din organisation.

![Azure AD-direktautentisering](./media/how-to-connect-pta/pta1.png)

Du kan kombinera direktautentisering med den [sömlös enkel inloggning](how-to-connect-sso.md) funktionen. Det här sättet när användarna har åtkomst till program på sina företagets datorer i företagsnätverket, behöver de inte att skriva in sina lösenord för inloggning.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Viktiga fördelar med att använda Azure AD-direktautentisering

- *En utmärkt användarupplevelse*
  - Användare använda samma lösenord för att logga in på både lokala och molnbaserade program.
  - Användarna snabbare kan prata IT supportavdelningen lösa lösenord-relaterade problem.
  - Användarna kan slutföra [egen lösenordshantering](../authentication/active-directory-passwords-overview.md) uppgifter i molnet.
- *Enkelt att distribuera och administrera*
  - Inget behov av komplicerade lokala distributioner eller nätverkskonfiguration.
  - Behöver en enkel agent är installerade på plats.
  - Inga hanteringskostnader för. Agenten får automatiskt förbättringar och felkorrigeringar.
- *Skydda*
  - Lokala lösenord lagras aldrig i molnet i någon form.
  - Skyddar dina användarkonton genom att arbeta smidigt med [principer för Azure AD villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md), inklusive Multi-Factor Authentication (MFA), [blockera äldre](../conditional-access/conditions.md) och av [ Filtrera bort råstyrkeattacker för lösenord](../authentication/howto-password-smart-lockout.md).
  - Agenten gör bara utgående anslutningar från ditt nätverk. Det finns därför inga krav på att installera agenten i ett perimeternätverk, även kallat DMZ.
  - Kommunikationen mellan en agent och Azure AD skyddas med certifikatbaserad autentisering. Dessa certifikat förnyas varje månaderna av Azure AD.
- *Med hög tillgänglighet*
  - Ytterligare agenter kan installeras på flera lokala servrar för att ge hög tillgänglighet för inloggningsförfrågningar.

## <a name="feature-highlights"></a>Funktionen visar

- Har stöd för användarinloggning i alla web webbläsarbaserade program och i Microsoft Office-klientprogram som använder [modern autentisering](https://aka.ms/modernauthga).
- Logga in användarnamn kan vara antingen en lokal Standardanvändarnamnet (`userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
- Funktionen fungerar sömlöst med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) funktioner, till exempel Multi-Factor Authentication (MFA) för att skydda dina användare.
- Integrerad med molnbaserade [egen lösenordshantering](../authentication/active-directory-passwords-overview.md), inklusive tillbakaskrivning till lokala Active Directory och lösenordsskydd av förbjuda vanliga lösenord.
- Miljöer med Multi-Forest stöds om det finns skogsförtroenden mellan AD-skogar och om routning är korrekt konfigurerad.
- Det är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.
- Den kan aktiveras [Azure AD Connect](whatis-hybrid-identity.md).
- Den använder en enkel lokal agent som lyssnar efter och svarar på begäranden för verifiering av lösenord.
- Installerar flera agenter ger hög tillgänglighet för inloggningsförfrågningar.
- Den [skyddar](../authentication/howto-password-smart-lockout.md) dina lokala konton mot brute force angrepp för lösenord i molnet.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart](how-to-connect-pta-quick-start.md) – komma igång och köra Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md) -konfigurera Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md) – förstå hur den här funktionen fungerar.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md) -svar på vanliga frågor och svar.
- [Felsöka](tshoot-connect-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [Djupgående om säkerhet](how-to-connect-pta-security-deep-dive.md) -mer djupgående teknisk information om funktionen.
- [Azure AD sömlös SSO](how-to-connect-sso.md) -mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.
