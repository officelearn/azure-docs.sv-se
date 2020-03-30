---
title: 'Azure AD Connect: Direktautentisering | Microsoft-dokument'
description: I den här artikeln beskrivs Azure Active Directory (Azure AD) direktautentisering och hur den tillåter Azure AD-inloggningar genom att validera användarnas lösenord mot lokala Active Directory.
services: active-directory
keywords: vad är Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
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
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185505"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Användarinloggning med Azure Active Directory-direktautentisering

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Vad är Azure Active Directory Direktautentisering?

Azure Active Directory (Azure AD) Direktautentisering gör att användarna kan logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse – ett lösenord mindre att komma ihåg, och kostnaderna för IT-supportavdelningen minskas eftersom användarna blir mindre benägna att glömma hur de ska logga in. När användare loggar in med Azure AD validerar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Den här funktionen är ett alternativ till [Azure AD Password Hash Synchronization](how-to-connect-password-hash-synchronization.md), som ger samma fördel av molnautentisering till organisationer. Vissa organisationer som vill tillämpa sina lokala active directory-säkerhets- och lösenordsprinciper kan dock välja att använda direktautentisering i stället. Granska [den här guiden](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) för en jämförelse av de olika Azure AD-inloggningsmetoderna och hur du väljer rätt inloggningsmetod för din organisation.

![Azure AD-direktautentisering](./media/how-to-connect-pta/pta1.png)

Du kan kombinera direktautentisering med funktionen [Sömlös enkel inloggning.](how-to-connect-sso.md) På så sätt behöver användarna inte skriva in sina lösenord när användarna använder program på sina företagsdatorer i företagets nätverk.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Viktiga fördelar med att använda Azure AD-direktautentisering

- *En utmärkt användarupplevelse*
  - Användare använder samma lösenord för att logga in på både lokala och molnbaserade program.
  - Användarna ägnar mindre tid åt att prata med IT-helpdesk för att lösa lösenordsrelaterade problem.
  - Användare kan utföra [lösenordshanteringsuppgifter för självbetjäning](../authentication/active-directory-passwords-overview.md) i molnet.
- *Lätt att distribuera & administrera*
  - Inget behov av komplexa lokala distributioner eller nätverkskonfiguration.
  - Behöver bara en lättviktsagent som ska installeras lokalt.
  - Inga hanteringskostnader. Agenten får automatiskt förbättringar och buggfixar.
- *Skydda*
  - Lokala lösenord lagras aldrig i molnet i någon form.
  - Skyddar dina användarkonton genom att arbeta sömlöst med [Azure AD Conditional Access-principer,](../active-directory-conditional-access-azure-portal.md)inklusive MFA (Multi-Factor Authentication), [blockera äldre autentisering](../conditional-access/concept-conditional-access-conditions.md) och genom [att filtrera bort brute force-lösenordsattacker](../authentication/howto-password-smart-lockout.md).
  - Agenten upprättar bara utgående anslutningar inifrån nätverket. Därför finns det inget krav på att installera agenten i ett perimeternätverk, även känt som en DMZ.
  - Kommunikationen mellan en agent och Azure AD skyddas med hjälp av certifikatbaserad autentisering. Dessa certifikat förnyas automatiskt med några månaders mellanrum av Azure AD.
- *Högt tillgänglighet*
  - Ytterligare agenter kan installeras på flera lokala servrar för att ge hög tillgänglighet för inloggningsbegäranden.

## <a name="feature-highlights"></a>Höjdpunkter i funktionen

- Stöder användarloggning i alla webbläsarbaserade program och i Microsoft Office-klientprogram som använder [modern autentisering](https://aka.ms/modernauthga).
- Inloggningsanvändarnamn kan vara antingen det lokala`userPrincipalName`standardanvändarnamnet ( ) eller ett annat `Alternate ID`attribut som konfigurerats i Azure AD Connect (kallas ).
- Funktionen fungerar sömlöst med [funktioner för villkorlig åtkomst,](../active-directory-conditional-access-azure-portal.md) till exempel MFA (Multi Factor Authentication) för att skydda användarna.
- Integrerad med molnbaserad [lösenordshantering med självbetjäning](../authentication/active-directory-passwords-overview.md), inklusive återställning av lösenord till lokala Active Directory och lösenordsskydd genom att förbjuda vanliga lösenord.
- Flerskogsmiljöer stöds om det finns skogsförtroenden mellan AD-skogarna och om namnsuffixdirigeringen är korrekt konfigurerad.
- Det är en kostnadsfri funktion och du behöver inga betalda utgåvor av Azure AD för att kunna använda den.
- Den kan aktiveras via [Azure AD Connect](whatis-hybrid-identity.md).
- Den använder en lätt lokal agent som lyssnar efter och svarar på begäranden om lösenordsverifiering.
- Installera flera agenter ger hög tillgänglighet för inloggningsbegäranden.
- Det [skyddar](../authentication/howto-password-smart-lockout.md) dina lokala konton mot brute force lösenordsattacker i molnet.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart](how-to-connect-pta-quick-start.md) – Komma igång med Azure AD-direktautentisering.
- [Migrera från AD FS till Direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md) - Konfigurera smart utelåsningsfunktion på din klient för att skydda användarkonton.
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md) - Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md) - Förstå hur den här funktionen fungerar.
- [Vanliga frågor](how-to-connect-pta-faq.md) och svar - Svar på vanliga frågor.
- [Felsöka](tshoot-connect-pass-through-authentication.md) - Lär dig hur du löser vanliga problem med funktionen.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - Ytterligare djup teknisk information om funktionen.
- [Azure AD Seamless SSO](how-to-connect-sso.md) – Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - För att lämna in nya funktionsförfrågningar.
