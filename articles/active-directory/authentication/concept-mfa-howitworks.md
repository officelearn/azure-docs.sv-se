---
title: Azure Multi-Factor Authentication - och hur det fungerar – Azure Active Directory
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
ms.openlocfilehash: fa25e8a965b89c4e97263e3767a9400079fcad7a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496794"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar det: Azure Multi-Factor Authentication

Säkerheten för tvåstegsverifiering ligger i dess lager. En stor utmaning anger att kompromissa med flera autentiseringsfaktorer för angripare. Även om någon lyckas få användarens lösenord, är det oanvändbara utan även tillgång till ytterligare en autentiseringsmetod. Det fungerar genom att kräva två eller fler av följande autentiseringsmetoder:

* Något du vet (normalt ett lösenord)
* Något du har (betrodda enheter som inte enkelt dupliceras, t.ex. en telefon)
* Något du är (biometrik)

<center>

![Konceptuell autentisering metoder bild](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) hjälper dig att skydda åtkomsten till data och program samtidigt som förenklar för användare. Det ger ökad säkerhet genom att kräva ett andra formen av autentisering och ger stark autentisering via en mängd lättanvända [autentiseringsmetoder](concept-authentication-methods.md). Användare kan eller inte kan angripas för MFA baserat på konfigurationsbeslut som administratören gör.

## <a name="how-to-get-multi-factor-authentication"></a>Hur du hämtar Multi-Factor Authentication?

Multi-Factor Authentication ingår i följande erbjudanden:

* **Azure Active Directory Premium** eller **Microsoft 365 Business** -fullständig användning av Azure Multi-Factor Authentication använder principer för villkorlig åtkomst för att kräva multifaktorautentisering.

* **Azure AD kostnadsfri**, **Azure AD Basic**, eller fristående **Office 365** licenser – Använd färdiga [principer för villkorlig åtkomst baslinje protection](../conditional-access/concept-baseline-protection.md) att kräva multifaktorautentisering för dina användare och administratörer.

* **Azure Active Directorys globala administratörer** -vissa Azure Multi-Factor Authentication-funktioner är tillgängliga som ett sätt att skydda globala administratörskonton.

> [!NOTE]
> Nya kunder kan inte längre att köpa Azure Multi-Factor Authentication som en fristående erbjuder effektiva September 1 2018. Multifaktorautentisering fortsätter att vara en tillgänglig funktion i Azure AD Premium-licenser.

## <a name="supportability"></a>Support

Eftersom de flesta användare är vana vid att använda endast lösenord för autentisering, är det viktigt att din organisation kommunicerar med alla användare om den här processen. Medvetenhet kan minska risken för att användare kontakta supportavdelningen för mindre allvarliga problem som rör MFA. Det finns dock vissa scenarier där tillfälligt inaktivera MFA är nödvändigt. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Träna supportpersonalen att hantera scenarier där användaren kan inte logga in eftersom de inte har åtkomst till sina autentiseringsmetoder eller de inte fungerar korrekt.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänsten, supportpersonal kan lägga till en användare till en grupp som inte ingår i en princip som kräver MFA.
* Överväg att använda villkorlig åtkomst – namngivna platser som ett sätt att minimera tvåstegsverifiering uppmanar. På så sätt kan administratörer kan kringgå tvåstegsverifiering för användare som loggar in från en säker betrodd nätverksplats, till exempel ett nätverk segment som används för den nya användaren onboarding.
* Distribuera [Azure AD Identity Protection](../active-directory-identityprotection.md) och utlöser tvåstegsverifiering baserat på riskhändelser.

## <a name="next-steps"></a>Nästa steg

- [Stegvis distribution av Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
