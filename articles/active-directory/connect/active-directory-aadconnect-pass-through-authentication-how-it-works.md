---
title: "Azure AD Connect: Direkt-autentisering - så här fungerar det? | Microsoft Docs"
description: "Den här artikeln beskriver hur Azure Active Directory direkt-autentisering fungerar."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: c7863e38671349b6424ee08330da8aaa49cb2a70
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory direkt-autentisering: Tekniska ingående
Följande artikel är en översikt över hur Azure AD direkt autentisering fungerar.  Djup tekniska och säkerhetsinformation finns i [ **säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) artikel.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hur fungerar Azure Active Directory direkt-autentisering?

När en användare försöker logga in på ett program som skyddas av Azure Active Directory (Azure AD), och om direkt-autentisering är aktiverat på klienten, utförs följande steg:

1. Användaren försöker komma åt ett program (till exempel Outlook Web App - https://outlook.office365.com/owa/).
2. Om användaren inte redan har signerats omdirigeras användaren till sidan för Azure AD.
3. Användaren anger sitt användarnamn och lösenord i Azure AD-inloggningssida och klickar på knappen ”Logga in”.
4. Azure AD på begäran inloggning placerar användarnamn och lösenord (krypterade med en offentlig nyckel) i en kö.
5. En lokal autentiseringsagent hämtar användarnamn och lösenord för krypterade från kön.
6. Agenten dekrypterar lösenord med dess privata nyckel.
7. Agenten verifierar sedan användarnamnet och lösenordet mot Active Directory använder standard Windows API: er (en liknande mekanism för som används av Active Directory Federation Services). Användarnamnet kan vara antingen lokalt Standardanvändarnamnet (vanligtvis `userPrincipalName`) eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
8. Den lokala Active Directory domänkontrollanten (DC) sedan utvärderar begäran och returnerar lämplig respons (lyckad, misslyckad, lösenord upphört att gälla eller användaren utelåst) till agenten.
9. Autentiseringsagent, returnerar svaret tillbaka till Azure AD.
10. Azure AD svaret utvärderas och svarar på användare efter behov – till exempel det loggar du in omedelbart eller förfrågningar för multi-Factor Authentication (MFA).
11. Om användaren loggar in lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och steg som ingår.

![Direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Nästa steg
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och körs direkt i Azure AD-autentisering.
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -ytterligare djupt teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
