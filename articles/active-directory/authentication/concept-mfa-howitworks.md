---
title: Så här fungerar Azure MFA – Azure Active Directory
description: Azures Multi-Factor Authentication bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess.
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
ms.openlocfilehash: 0982f6fb70cd6866af48feab640d5dc36bcb6b28
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848688"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar det: Azure Multi-Factor Authentication

Säkerheten för tvåstegsverifiering är i sitt lager tillvägagångs sätt. Att kompromissa med flera autentiseringsmetoder innebär en betydande utmaning för angripare. Även om en angripare hanterar användarens lösen ord är det oanvändbart utan att du har till gång till ytterligare en autentiseringsmetod. Det fungerar genom att kräva två eller fler av följande autentiseringsmetoder:

* Något som du känner till (vanligt vis ett lösen ord)
* Något du har (en betrodd enhet som inte enkelt dupliceras, till exempel en telefon)
* Något som du är (biometrik)

<center>

avbildning av ![konceptuella autentiseringsmetoder](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) hjälper till att skydda åtkomsten till data och program samtidigt som användarnas skull bibehålls. Den ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering via en rad enkla att använda [autentiseringsmetoder](concept-authentication-methods.md). Användare kan kanske inte anropas för MFA baserat på konfigurations beslut som en administratör gör.

## <a name="how-to-get-multi-factor-authentication"></a>Hur skaffar jag multifaktorautentisering?

Multifaktorautentisering ingår i följande erbjudanden:

* **Azure Active Directory Premium** eller **Microsoft 365 Business** fullständig användning av Azure Multi-Factor Authentication med hjälp av principer för villkorlig åtkomst för att kräva Multi-Factor Authentication.

* **Azure AD Free** eller fristående **Office 365** -licenser – Använd i förväg skapade [bas linje skydds principer för villkorlig åtkomst](../conditional-access/concept-baseline-protection.md) för att kräva Multi-Factor Authentication för dina användare och administratörer.

* **Azure Active Directory Global Administrators** – en delmängd av funktionerna i Azure Multi-Factor Authentication är tillgängliga som ett sätt att skydda konton för globala administratörer.

> [!NOTE]
> Nya kunder kan inte längre köpa Azure Multi-Factor Authentication som ett fristående erbjudande från den 1 september 2018. Multi-Factor Authentication kommer att fortsätta vara en tillgänglig funktion i Azure AD Premium licenser.

## <a name="supportability"></a>Support

Eftersom de flesta användare bara är vana vid att använda lösen ord för att autentisera, är det viktigt att din organisation kommunicerar med alla användare om den här processen. Medvetenheten kan minska sannolikheten för att användarna kontaktar supportavdelningen för mindre problem som rör MFA. Det finns dock några scenarier där det krävs tillfälligt inaktive ring av MFA. Använd följande rikt linjer för att förstå hur du hanterar dessa scenarier:

* Utbilda support personalen för att hantera scenarier där användaren inte kan logga in eftersom de inte har åtkomst till sina autentiseringsmetoder eller inte fungerar som de ska.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänsten kan support personalen lägga till en användare i en grupp som är exkluderad från en princip som kräver MFA.
* Överväg att använda villkorlig åtkomst med namngivna platser som ett sätt att minimera två stegs verifierings instruktioner. Med den här funktionen kan administratörer kringgå tvåstegsverifiering för användare som loggar in från en säker betrodd nätverks plats, till exempel ett nätverks segment som används för nya användar onboarding.
* Distribuera [Azure AD Identity Protection](../active-directory-identityprotection.md) och utlösa tvåstegsverifiering baserat på risk identifieringar.

## <a name="next-steps"></a>Nästa steg

- [Steg-för-steg-distribution av Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
