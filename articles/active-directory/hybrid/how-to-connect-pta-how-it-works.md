---
title: 'Azure AD Connect: Direktautentisering – hur det fungerar | Microsoft Docs'
description: Den här artikeln beskriver hur Azure Active Directory-direktautentisering fungerar
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: f24930d31e8435a00fec6a449445ea4682deea8f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169356"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory-direktautentisering: Teknisk djupdykning
Den här artikeln är en översikt över hur fungerar med Azure Active directory (Azure AD)-direktautentisering. Djup teknisk och säkerhetsinformation finns i den [djupgående om säkerhet](how-to-connect-pta-security-deep-dive.md) artikeln.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hur fungerar Azure Active Directory-direktautentisering?

>[!NOTE]
>En nödvändig förutsättning för direktautentisering ska fungera måste användare etableras i Azure AD från en lokal Active Directory med Azure AD Connect. Direktautentisering gäller inte för molnexklusiva användare.

När en användare försöker logga in på ett program som skyddas av Azure AD, och om direktautentisering är aktiverad på klienten, genomförs följande steg:

1. Användaren försöker få åtkomst till ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa/).
2. Om du inte redan har signerats användaren omdirigeras till Azure AD **användarinloggning** sidan.
3. Användaren anger sina användarnamn i Azure AD-inloggningssidan och väljer sedan den **nästa** knappen.
4. Användaren anger sitt lösenord i Azure AD-inloggningssidan och väljer sedan den **logga in** knappen.
5. Azure AD när tas emot begäran om att logga in, placerar det användarnamn och lösenord (krypterade med hjälp av den offentliga nyckeln för autentisering-agenter) i en kö.
6. En Agent för lokal autentisering hämtar användarnamn och lösenord för krypterade från kön. Observera att agenten inte ofta söka efter begäranden från kön, men hämtar förfrågningar via en i förväg upprättade beständig anslutning.
7. Agenten dekrypterar lösenordet med hjälp av dess privata nyckel.
8. Agenten validerar användarnamnet och lösenordet mot Active Directory med hjälp av standard Windows API: er, vilket är en liknande mekanism för vilka Active Directory Federation Services (AD FS) använder. Användarnamnet kan vara antingen en lokal Standardanvändarnamnet, vanligtvis `userPrincipalName`, eller ett annat attribut som konfigurerats i Azure AD Connect (kallas även `Alternate ID`).
9. Den lokala Active Directory-domänkontrollant (DC) utvärderar begäran och returnerar lämpligt svar (lyckad, misslyckad, lösenord har upphört att gälla eller användare som har låsts ute) till agenten.
10. Autentiseringsagent, returnerar svaret tillbaka till Azure AD.
11. Azure AD utvärderar svaret och svarar på användaren efter behov. Till exempel Azure AD antingen direkt loggar du in eller begäranden för Azure Multi-Factor Authentication.
12. Om användaren-inloggningen lyckas, kan användaren komma åt programmet.

Följande diagram illustrerar komponenterna och steg som ingår:

![Direktautentisering](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Läs om vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](how-to-connect-pta-quick-start.md): Kom igång på Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://aka.ms/adfstoPTADP) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Konfigurera funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): Få svar på vanliga frågor och svar.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](how-to-connect-pta-security-deep-dive.md): Få djupgående teknisk information om funktionen direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet till filen nya funktionbegäran.

