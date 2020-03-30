---
title: Azure MultiFaktor-autentisering för din organisation - Azure Active Directory
description: Lär dig mer om de tillgängliga funktionerna i Azure Multi-Factor Authentication för din organisation baserat på din licensmodell
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530401"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Översikt över Azure Multi Factor-autentisering för din organisation

Det finns flera sätt att aktivera Azure Multi-Factor Authentication för dina Azure Active Directory -användare (AD) baserat på de licenser som din organisation äger. 

![Undersöka signaler och genomdriva MFA om det behövs](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Baserat på våra studier är ditt konto mer än 99,9 % mindre sannolikt att äventyras om du använder multifaktorautentisering (MFA).

Så hur din organisation slå på MFA även gratis, innan de blir en statistik?

## <a name="free-option"></a>Gratis alternativ

Kunder som använder de kostnadsfria fördelarna med Azure AD kan använda [säkerhetsstandarder](../fundamentals/concept-fundamentals-security-defaults.md) för att aktivera multifaktorautentisering i sin miljö.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 eller E5

För kunder med Office 365 finns det två alternativ:

* Azure Multi-Factor Authentication är antingen aktiverat eller inaktiverat för alla användare, för alla inloggningshändelser. Det finns ingen möjlighet att bara aktivera multifaktorautentisering för en delmängd av användare, eller bara under vissa scenarier. Ledningen sker via Office 365-portalen. 
* För en förbättrad användarupplevelse, uppgradera till Azure AD Premium P1 eller P2 och använda villkorlig åtkomst. Mer information finns i säkra Office 365-resurser med multifaktorautentisering.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

För kunder med Azure AD Premium P1 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E3, Microsoft 365 F1 eller Microsoft 365 E3: 

Använd [Azure AD-villkorlig åtkomst](../conditional-access/overview.md) för att fråga användare om multifaktorautentisering under vissa scenarier eller händelser som passar dina affärskrav.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

För kunder med Azure AD Premium P2 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E5 eller Microsoft 365 E5: 

Ger den starkaste säkerhetspositionen och förbättrad användarupplevelse. Lägger till [riskbaserad villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-risk.md) till Azure AD Premium P1-funktionerna som anpassar sig till användarens mönster och minimerar autentiseringsmeddelanden med flera faktorer.

## <a name="authentication-methods"></a>Autentiseringsmetoder

|   | Standardinställningar för säkerhet | Alla andra metoder |
| --- | --- | --- |
| Avisering via mobilapp | X | X |
| Verifieringskod från mobilapp eller maskinvarutoken |   | X |
| Sms till telefon |   | X |
| Ringa till telefon |   | X |

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i självstudien för att [skydda användarloggningshändelser med Azure Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Mer information om licensiering finns i [Funktioner och licenser för Azure Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
