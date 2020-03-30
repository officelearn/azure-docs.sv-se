---
title: Vad är läge för endast villkorsåtkomstrapport? - Azure Active Directory
description: Hur kan rapport-bara läge hjälp med villkorlig åtkomst principdistribution
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295287"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Vad är läge för endast villkorsåtkomstrapport?

Villkorlig åtkomst används ofta av våra kunder för att hålla sig säkra genom att tillämpa rätt åtkomstkontroller under rätt omständigheter. Men en av utmaningarna med att distribuera en princip för villkorlig åtkomst i organisationen är att avgöra effekten för slutanvändarna. Det kan vara svårt att förutse antalet användare och namn som påverkas av vanliga distributionsinitiativ, till exempel blockera äldre autentisering, kräva multifaktorautentisering för en användares population eller implementera inloggningsriskprinciper. 

Endast rapportläge är ett nytt principtillstånd för villkorlig åtkomst som gör det möjligt för administratörer att utvärdera effekten av principer för villkorlig åtkomst innan de aktiveras i sin miljö.  Med lanseringen av endast rapportläge:

- Principer för villkorlig åtkomst kan aktiveras i endast rapportläge.
- Under inloggningen utvärderas principer i endast rapportläge men tillämpas inte.
- Resultaten loggas på flikarna **Endast villkorlig åtkomst** och Rapport **(Förhandsversion)** i inloggningsuppgifterna för inloggningsuppgifter.
- Kunder med en Azure Monitor-prenumeration kan övervaka effekten av sina principer för villkorlig åtkomst med hjälp av arbetsboken för insikter om villkorlig åtkomst.

> [!WARNING]
> Principer i endast rapportläge som kräver kompatibla enheter kan uppmana användare på Mac, iOS och Android att välja ett enhetscertifikat under principutvärderingen, även om enhetsefterlevnad inte tillämpas. Dessa uppmaningar kan upprepas tills enheten har gjorts kompatibel. För att förhindra att slutanvändare får uppmaningar under inloggningen utesluter du enhetsplattformar Mac, iOS och Android från principer för endast rapporter som utför kontroller av enhetsefterlevnad.

![Fliken Endast rapport i inloggningsloggen för Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Politiska resultat

När en princip i endast rapportläge utvärderas för en viss inloggning finns det fyra nya möjliga resultatvärden:

| Resultat | Beskrivning |
| --- | --- |
| Endast rapport: Framgång | Alla konfigurerade principvillkor, obligatoriska icke-interaktiva bidragskontroller och sessionskontroller var uppfyllda. Ett multifaktorautentiseringskrav uppfylls till exempel av ett MFA-anspråk som redan finns i token, eller så uppfylls en kompatibel enhetsprincip genom att utföra en enhetskontroll på en kompatibel enhet. |
| Endast rapport: Fel | Alla konfigurerade principvillkor var uppfyllda, men inte alla nödvändiga icke-interaktiva bidragskontroller eller sessionskontroller var uppfyllda. En princip gäller till exempel för en användare där en blockkontroll har konfigurerats eller en enhet misslyckas med en kompatibel enhetsprincip. |
| Endast rapport: Användaråtgärd krävs | Alla konfigurerade principvillkor var uppfyllda, men användaråtgärden skulle krävas för att uppfylla de nödvändiga bidragskontrollerna eller sessionskontrollerna. Med endast rapportläge uppmanas användaren inte att uppfylla de kontroller som krävs. Användare uppmanas till exempel inte för multifaktorautentiseringsutmaningar eller användningsvillkor.   |
| Endast rapport: Tillämpas inte | Alla konfigurerade principvillkor var inte uppfyllda. Användaren är till exempel utesluten från principen eller principen gäller endast för vissa betrodda namngivna platser. |

## <a name="conditional-access-insights-workbook"></a>Arbetsbok för Statistik för villkorsstyrda accesser

Administratörer har möjlighet att skapa flera principer i endast rapportläge, så det är nödvändigt att förstå både den individuella effekten av varje princip och den kombinerade effekten av flera principer som utvärderas tillsammans. Den nya arbetsboken Conditional Access Insights gör det möjligt för administratörer att visualisera frågor om villkorlig åtkomst och övervaka effekten av en princip för ett visst tidsintervall, uppsättning program och användare. 
 
## <a name="next-steps"></a>Nästa steg

[Konfigurera endast rapportläge för en princip för villkorlig åtkomst](howto-conditional-access-report-only.md)
