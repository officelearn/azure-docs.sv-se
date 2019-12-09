---
title: Aktivera Multi-Factor Authentication för din organisation – Azure Active Directory
description: Aktivera Azure MFA för din organisation baserat på din licens
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932455"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Aktivera Multi-Factor Authentication för din organisation

Det finns flera sätt att aktivera Azure Multi-Factor Authentication (MFA) för dina Azure Active Directory (AD)-användare baserat på de licenser som organisationen äger. 

![Undersök signaler och Använd MFA vid behov](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Utifrån våra studier är ditt konto mer än 99,9% mindre troligt att bli komprometterat om du använder MFA.

Hur aktiverar din organisation även Multi-Factor Authentication innan den blir en statistik?

## <a name="free-option"></a>Kostnads fritt alternativ

Kunder som utnyttjar de kostnads fria fördelarna med Azure AD kan använda [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md) för att aktivera Multi-Factor Authentication i sin miljö.

## <a name="office-365"></a>Office 365

För kunder med Office 365 finns det två alternativ:

- [Säkerhets inställningar](concept-fundamentals-security-defaults.md) kan aktive ras via Azure AD för att skydda alla användare med Azure Multi-Factor Authentication.
- Om din organisation kräver mer granularitet i att tillhandahålla Multi-Factor Authentication, inkluderar dina Office [-licenser ANVÄNDARSPECIFIKA MFA-](../authentication/howto-mfa-userstates.md) funktioner. MFA per användare aktive ras och tillämpas på varje användare individuellt av administratörer.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

För kunder med Azure AD Premium P1 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E3, Microsoft 365 F1 eller Microsoft 365 E3: 

Rekommendationen är att använda [principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md) för den bästa användar upplevelsen.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

För kunder med Azure AD Premium P2 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E5 eller Microsoft 365 E5: 

Rekommendationen är att använda [principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md) tillsammans med [identitets skydds](../identity-protection/overview-v2.md) risk principer för bästa möjliga användar upplevelse och Tvingad flexibilitet.

## <a name="authentication-methods"></a>Autentiseringsmetoder

|   | Standardinställningar för säkerhet | Alla andra metoder |
| --- | --- | --- |
| Meddelande via mobilapp | X | X |
| Verifieringskod från mobilapp eller maskinvarutoken |   | X |
| Textmeddelande till telefon |   | X |
| Samtal till telefon |   | X |
| Applösenord |   | X * * |

\* * Applösenord är bara tillgängliga i MFA per användare med äldre autentiseringar endast om de aktive ras av administratörer.

## <a name="next-steps"></a>Nästa steg

[Prissättnings sida för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
