---
title: 'Azure AD Connect: Direktautentisering – så fungerar det | Microsoft-dokument'
description: I den här artikeln beskrivs hur Azure Active Directory-direktautentisering fungerar
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59cd52dbdf6c13900cde592aeb52d8bf9abf850f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347792"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory Direktautentisering: Teknisk djupdykning
Den här artikeln är en översikt över hur Azure Active Directory (Azure AD) Direktautentisering fungerar. Djup teknisk information och säkerhetsinformation finns i artikeln [Säkerhet djupdykning.](how-to-connect-pta-security-deep-dive.md)

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hur fungerar Azure Active Directory Pass-through-autentisering?

>[!NOTE]
>Som en förutsättning för direktautentisering för att fungera måste användare etableras i Azure AD från lokal Active Directory med Azure AD Connect. Direktautentisering gäller inte användare som endast är molnet.

När en användare försöker logga in på ett program som skyddas av Azure AD, och om direktautentisering är aktiverad på klienten, inträffar följande steg:

1. Användaren försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa/).
2. Om användaren inte redan är inloggad omdirigeras användaren till sidan för Azure **AD-användare.**
3. Användaren anger sitt användarnamn på inloggningssidan för Azure AD och väljer sedan knappen **Nästa.**
4. Användaren anger sitt lösenord på inloggningssidan för Azure AD **Sign in** och väljer sedan inloggningsknappen.
5. Azure AD, när du tar emot begäran om att logga in, placerar användarnamn och lösenord (krypteras med hjälp av den offentliga nyckeln för autentiseringsagenter) i en kö.
6. En lokal autentiseringsagent hämtar användarnamnet och det krypterade lösenordet från kön. Observera att agenten inte ofta avsöker efter begäranden från kön, men hämtar begäranden via en ilagd beständig anslutning.
7. Agenten dekrypterar lösenordet med hjälp av dess privata nyckel.
8. Agenten validerar användarnamnet och lösenordet mot Active Directory med hjälp av vanliga Windows-API:er, vilket är en liknande mekanism som vad AD FS (Active Directory Federation Services) använder. Användarnamnet kan vara antingen det lokala `userPrincipalName`standardanvändarnamnet, vanligtvis, eller ett annat `Alternate ID`attribut som konfigurerats i Azure AD Connect (kallas ).
9. Den lokala Active Directory-domänkontrollanten (DC) utvärderar begäran och returnerar lämpligt svar (lyckades, fel, lösenord har upphört att gälla eller användaren utelåst) till agenten.
10. Autentiseringsagenten returnerar i sin tur det här svaret tillbaka till Azure AD.
11. Azure AD utvärderar svaret och svarar på användaren efter behov. Azure AD signerar till exempel antingen användaren omedelbart eller begäranden om Azure Multi-Factor Authentication.
12. Om användarloggningen lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och steg som ingår:

![Direktautentisering](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabbstart:](how-to-connect-pta-quick-start.md)Komma igång med Azure AD Pass-through-autentisering.
- [Migrera från AD FS till Direktautentisering](https://aka.ms/adfstoPTADP) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Utelåsning:](../authentication/howto-password-smart-lockout.md)Konfigurera smart utelåsningsfunktionen på din klient för att skydda användarkonton.
- [Vanliga frågor](how-to-connect-pta-faq.md): Hitta svar på vanliga frågor.
- [Felsöka:](tshoot-connect-pass-through-authentication.md)Lär dig hur du löser vanliga problem med direktautentiseringsfunktionen.
- [Security Deep Dive:](how-to-connect-pta-security-deep-dive.md)Få djup teknisk information om funktionen Direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory Forum för att fila nya funktionsbegäranden.

