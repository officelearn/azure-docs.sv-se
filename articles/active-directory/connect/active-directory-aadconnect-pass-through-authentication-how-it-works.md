---
title: 'Azure AD Connect: Direkt-autentisering - hur det fungerar | Microsoft Docs'
description: "Den här artikeln beskriver hur fungerar Azure Active Directory direkt-autentisering"
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: billmath
ms.openlocfilehash: cd42278048b8162a06af21de04397a959be33586
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory direkt-autentisering: Tekniska ingående
Den här artikeln är en översikt över hur Azure Active directory (AD Azure) direkt autentisering fungerar. Djup tekniska och säkerhetsinformation finns i [säkerhet ingående](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) artikel.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hur fungerar Azure Active Directory direkt-autentisering?

När en användare försöker logga in på ett program som skyddas av Azure AD och direkt-autentisering är aktiverat på klienten, utförs följande steg:

1. Användaren försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa/).
2. Om användaren inte redan har signerats användaren omdirigeras till Azure AD **användarinloggning** sidan.
3. Användaren anger sitt användarnamn och lösenord i Azure AD-inloggningssidan och väljer sedan den **inloggning** knappen.
4. Azure AD på begäran att logga in, placerar användarnamn och lösenord (krypterade med hjälp av en offentlig nyckel) i en kö.
5. En lokal autentiseringsagent hämtar användarnamn och lösenord för krypterade från kön. Observera att agenten inte ofta avsökas begäranden från kön, men hämtar begäranden via en i förväg upprättade persistant-anslutning.
6. Agenten dekrypterar lösenordet med dess privata nyckel.
7. Agenten verifierar användarnamnet och lösenordet mot Active Directory med hjälp av standard Windows API: er, vilket är en liknande mekanism för vilka Active Directory Federation Services (AD FS) använder. Användarnamnet kan vara antingen lokalt Standardanvändarnamnet, vanligtvis `userPrincipalName`, eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
8. Lokala Active Directory-domänkontrollant (DC) utvärderar begäran och returnerar lämplig respons (lyckad, misslyckad, lösenord upphört att gälla eller användaren utelåst) till agenten.
9. Autentiseringsagent, returnerar svaret tillbaka till Azure AD.
10. Azure AD svaret utvärderas och svarar på användare efter behov. Till exempel Azure AD antingen loggar du in omedelbart eller förfrågningar om Azure Multi-Factor Authentication.
11. Om användaren loggar in lyckas, kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och steg som ingår:

![Direktautentisering](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång direkt i Azure AD för autentisering.
- [Smartkort kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Konfigurera Smart kontoutelåsning kapaciteten på din klient skydda användarkonton.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor och svar.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direkt-autentisering.
- [Säkerhet ingående](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): avancerad teknisk information om funktionen direkt-autentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Lär dig mer om den här funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.

