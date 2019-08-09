---
title: Azure Multi-Factor Authentication – hur det fungerar – Azure Active Directory
description: Azures Multi-Factor Authentication bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d4f19c16149dbed34c16d021ff7100cace9a06a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879149"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar det: Azure Multi-Factor Authentication

Säkerheten för tvåstegsverifiering är i sitt lager tillvägagångs sätt. Att kompromissa med flera autentiseringsmetoder innebär en betydande utmaning för angripare. Även om en angripare hanterar användarens lösen ord är det oanvändbart utan att du har till gång till ytterligare en autentiseringsmetod. Det fungerar genom att kräva två eller fler av följande autentiseringsmetoder:

* Något som du känner till (vanligt vis ett lösen ord)
* Något du har (en betrodd enhet som inte enkelt dupliceras, till exempel en telefon)
* Något som du är (biometrik)

<center>

![Bild av metoder för konceptuell autentisering](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) hjälper till att skydda åtkomsten till data och program samtidigt som användarnas skull bibehålls. Den ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering via en rad enkla att använda [autentiseringsmetoder](concept-authentication-methods.md). Användare kan kanske inte anropas för MFA baserat på konfigurations beslut som en administratör gör.

## <a name="how-to-get-multi-factor-authentication"></a>Hur skaffar du Multi-Factor Authentication?

Multi-Factor Authentication levereras som en del av följande erbjudanden:

* **Azure Active Directory Premium** eller **Microsoft 365 Business** fullständig användning av Azure Multi-Factor Authentication med principer för villkorlig åtkomst för att kräva Multi-Factor Authentication.

* **Azure AD Free** eller fristående **Office 365** -licenser – Använd i förväg skapade [bas linje skydds principer för villkorlig åtkomst](../conditional-access/concept-baseline-protection.md) för att kräva Multi-Factor Authentication för dina användare och administratörer.

* **Azure Active Directory globala administratörer** – en delmängd av Azure Multi-Factor Authentication-funktionerna är tillgängliga som ett sätt att skydda globala administratörs konton.

> [!NOTE]
> Nya kunder kanske inte längre köper Azure Multi-Factor Authentication som ett fristående erbjudande från den 1 september 2018. Multi-Factor Authentication kommer att fortsätta vara en tillgänglig funktion i Azure AD Premium licenser.

## <a name="supportability"></a>Support

Eftersom de flesta användare bara är vana vid att använda lösen ord för att autentisera, är det viktigt att din organisation kommunicerar med alla användare om den här processen. Medvetenheten kan minska sannolikheten för att användarna kontaktar supportavdelningen för mindre problem som rör MFA. Det finns dock några scenarier där det krävs tillfälligt inaktive ring av MFA. Använd följande rikt linjer för att förstå hur du hanterar dessa scenarier:

* Utbilda support personalen för att hantera scenarier där användaren inte kan logga in eftersom de inte har åtkomst till sina autentiseringsmetoder eller inte fungerar som de ska.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänsten kan support personalen lägga till en användare i en grupp som är exkluderad från en princip som kräver MFA.
* Överväg att använda villkorlig åtkomst med namngivna platser som ett sätt att minimera två stegs verifierings instruktioner. Med den här funktionen kan administratörer kringgå tvåstegsverifiering för användare som loggar in från en säker betrodd nätverks plats, till exempel ett nätverks segment som används för nya användar onboarding.
* Distribuera [Azure AD Identity Protection](../active-directory-identityprotection.md) och utlösa tvåstegsverifiering baserat på risk händelser.

## <a name="next-steps"></a>Nästa steg

- [Stegvis distribution av Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
