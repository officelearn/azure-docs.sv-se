---
title: Azure Multi-Factor Authentication - hur det fungerar
description: Azures Multi-Factor Authentication bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ad9d517be930f68dcddba87fc59eab8b830a2b1c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159159"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar det: Azure Multi-Factor Authentication

Säkerheten för tvåstegsverifiering ligger i dess lager. En stor utmaning anger att kompromissa med flera autentiseringsfaktorer för angripare. Även om någon lyckas få användarens lösenord, är det oanvändbara utan även tillgång till ytterligare en autentiseringsmetod. Det fungerar genom att kräva två eller fler av följande autentiseringsmetoder:

* Något du vet (normalt ett lösenord)
* Något du har (betrodda enheter som inte enkelt dupliceras, t.ex. en telefon)
* Något du är (biometrik)

<center>![Konceptuell autentisering metoder bild](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) hjälper dig att skydda åtkomsten till data och program samtidigt som förenklar för användare. Det ger ökad säkerhet genom att kräva ett andra formen av autentisering och ger stark autentisering via en mängd lättanvända [autentiseringsmetoder](concept-authentication-methods.md).

## <a name="how-to-get-multi-factor-authentication"></a>Hur du hämtar Multi-Factor Authentication?

Multi-Factor Authentication ingår i följande erbjudanden:

* **Azure Active Directory Premium-licenser** -fullständig användning av Azure Multi-Factor Authentication-tjänsten (moln) eller Azure Multi-Factor Authentication Server (lokalt).
   * **Azure MFA Service (molnet)** - **det här alternativet är den rekommendera sökvägen för nya distributioner**. Azure MFA i molnet kräver någon lokal infrastruktur och kan användas med din federerad eller endast molnbaserade användare.
   * **Azure MFA Server** – om din organisation vill hantera tillhörande infrastruktur-element och har distribuerat AD FS i din miljö på molntjänstprenumeration på så sätt kan vara ett alternativ.
* **Multi-Factor Authentication för Office 365** -vissa Azure Multi-Factor Authentication-funktioner är tillgängliga som en del av din prenumeration. Mer information om MFA för Office 365 finns i artikeln [planera för multi-Factor authentication för Office 365-distributioner](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Azure Active Directorys globala administratörer** -vissa Azure Multi-Factor Authentication-funktioner är tillgängliga som ett sätt att skydda globala administratörskonton.

### <a name="auth-provider-or-mfa-license"></a>Auth-Provider eller MFA-licens

Om du har Azure AD Premium eller ett licens-paket som innehåller Azure AD Premium kan har du redan Azure MFA. Din organisation behöver inte något mer att utöka kapaciteten för tvåstegsverifiering verifiering för alla användare. Du behöver bara tilldela en licens till en användare och sedan kan du aktivera MFA.

Om du inte har licenser som inkluderar Azure MFA eller inte har tillräckligt med licenser för alla användare, kan du skapa en [MFA Auth-Provider](concept-mfa-authprovider.md) att utöka alla funktioner i MFA till de användare som behövs. 

> [!IMPORTANT]
> Om du inte har tillräckligt med licenser för alla användare, kan du skapa en per användare Multi-Factor Auth-Provider för att täcka resten av din organisation. Skapa inte en per autentisering Multi-Factor Auth-Provider. Om du gör det, kan du få betala för begäranden från användare som redan har licenser.

## <a name="supportability"></a>Support

Eftersom de flesta användare är vana vid att använda endast lösenord för autentisering, är det viktigt att din organisation kommunicerar med alla användare om den här processen. Medvetenhet kan minska risken för att användare kontakta supportavdelningen för mindre allvarliga problem som rör MFA. Det finns dock vissa scenarier där tillfälligt inaktivera MFA är nödvändigt. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Träna supportpersonalen att hantera scenarier där användaren kan inte logga in eftersom de inte har åtkomst till sina autentiseringsmetoder eller de inte fungerar korrekt.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänsten, supportpersonal kan lägga till en användare till en grupp som inte ingår i en princip som kräver MFA.
   * Supportpersonalen kan aktivera en tillfällig engångsförbikoppling för Azure MFA Server användarna att låta en användare kan autentisera utan tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder.
   * Med principer för villkorlig åtkomst för Azure MFA-tjänsten supportpersonal kan lägga till en användare till en grupp som inte ingår i en princip som kräver MFA.
* Överväg att använda tillförlitliga IP-adresser eller namngivna platser som ett sätt att minimera tvåstegsverifiering verifiering anvisningarna. Med den här funktionen kan administratörer för en hanterad eller federerade klient kringgå tvåstegsverifiering för användare som loggar in från en betrodd nätverksplats, till exempel en organisations intranät.
* Distribuera [Azure AD Identity Protection](../active-directory-identityprotection.md) och utlöser tvåstegsverifiering baserat på riskhändelser.

## <a name="next-steps"></a>Nästa steg

- Hitta information om [licensiering dina användare](concept-mfa-licensing.md)

- Få information om [vilken version som ska distribuera](concept-mfa-whichversion.md)

- Få svar på [vanliga frågor och svar](multi-factor-authentication-faq.md)