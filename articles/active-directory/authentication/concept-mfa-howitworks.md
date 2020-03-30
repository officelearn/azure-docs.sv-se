---
title: Så här fungerar Azure MFA - Azure Active Directory
description: Azure Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som användarnas efterfrågan på en enkel inloggningsprocess tillgodoses.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484068"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar Azure Multi-Factor Authentication

Säkerheten för tvåstegsverifiering ligger i dess skiktade tillvägagångssätt. Att kompromissa med flera autentiseringsfaktorer innebär en stor utmaning för angripare. Även om en angripare lyckas lära sig användarens lösenord, är det meningslöst utan att också ha innehav av ytterligare autentiseringsmetod. Det fungerar genom att kräva två eller flera av följande autentiseringsmetoder:

* Något du vet (vanligtvis ett lösenord)
* Något du har (en betrodd enhet som inte är lätt att duplicera, som en telefon)
* Något du är (biometri)

<center>

![Bild av begreppsmässiga autentiseringsmetoder](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) hjälper till att skydda åtkomsten till data och program samtidigt som enkelheten upprätthålls för användarna. Det ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering via en rad lättanvända [autentiseringsmetoder](concept-authentication-methods.md). Användare kan eller inte kan ifrågasättas för MFA baserat på konfigurationsbeslut som en administratör gör.

## <a name="how-to-get-multi-factor-authentication"></a>Hur skaffar jag multifaktorautentisering?

Multifaktorautentisering ingår i följande erbjudanden:

* **Azure Active Directory Premium** eller **Microsoft 365 Business** – Fullständig användning av Azure Multi-Factor Authentication med principer för villkorlig åtkomst för att kräva multifaktorautentisering.

* **Azure AD Free** eller fristående **Office 365-licenser** – Använd [säkerhetsstandarder](../fundamentals/concept-fundamentals-security-defaults.md) för att kräva multifaktorautentisering för användare och administratörer.

* **Azure Active Directory Global Administrators** – en delmängd av funktionerna i Azure Multi-Factor Authentication är tillgängliga som ett sätt att skydda konton för globala administratörer.

> [!NOTE]
> Nya kunder kanske inte längre köper Azure Multi-Factor Authentication som ett fristående erbjudande från och med den 1 september 2018. Multifaktorautentisering fortsätter att vara en tillgänglig funktion i Azure AD Premium-licenser.

## <a name="supportability"></a>Support

Eftersom de flesta användare är vana vid att bara använda lösenord för att autentisera, är det viktigt att din organisation kommunicerar med alla användare om den här processen. Medvetenhet kan minska sannolikheten för att användarna ringer din helpdesk för mindre problem relaterade till MFA. Det finns dock vissa scenarier där det är nödvändigt att tillfälligt inaktivera MFA. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Träna supportpersonalen att hantera scenarier där användaren inte kan logga in eftersom de inte har åtkomst till sina autentiseringsmetoder eller om de inte fungerar korrekt.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänst kan supportpersonalen lägga till en användare i en grupp som är undantagen från en princip som kräver MFA.
* Överväg att använda villkorlig åtkomst namngivna platser som ett sätt att minimera tvåstegsverifieringsuppmaningar. Med den här funktionen kan administratörer kringgå tvåstegsverifiering för användare som loggar in från en säker plats för betrodda nätverk, till exempel ett nätverkssegment som används för ny introduktion av användare.
* Distribuera [Azure AD Identity Protection](../active-directory-identityprotection.md) och utlösa tvåstegsverifiering baserat på riskidentifieringar.

## <a name="next-steps"></a>Nästa steg

- [Distribution av Azure Multi Factor-autentisering steg för steg](howto-mfa-getstarted.md)
