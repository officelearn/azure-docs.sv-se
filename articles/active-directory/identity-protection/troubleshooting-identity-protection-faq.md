---
title: Vanliga frågor och svar om identitets skydd i Azure Active Directory
description: Vanliga frågor och svar Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d22973867782ddb64ced2ac95e84c0b27a3addd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887603"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Vanliga frågor om identitets skydd i Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorera kända problem med användar risker

**Ignorera användar risk** i klassiskt identitets skydd ställer in aktören i användarens risk historik i identitets skydd till **Azure AD**.

**Ignorera användar risk** i identitets skydd anger aktören i användarens risk historik i identitets skydd till **\<administratörens namn med en hyperlänk som pekar på användarens blad\>** .

Det finns ett aktuellt känt problem som orsakar svars tider i det avstängda användar risk flödet. Om du har en "användar risk princip" slutar den här principen att gälla för avstängda användare i minuter att klicka på "Stäng användar risk". Det finns dock kända fördröjningar med UXen som uppdaterar "risk tillstånd" för avstängda användare. Som en lösning kan du uppdatera sidan på webb läsar nivån för att se den senaste användaren "risk status".

## <a name="risky-users-report-known-issues"></a>Riskfyllda användare rapportera kända problem

Frågor i fältet **username** är Skift läges känsliga, medan frågor i fältet **namn** är Case-oberoende.

Om du omväxlar **visas datum som** döljer den **senaste uppdaterade** kolumnen. Om du vill läsa kolumnen klickar du på **kolumner** överst på bladet riskfyllda användare.

**Ignorera alla händelser** i klassiskt identitets skydd anger status för risk identifieringar till **stängda (löst)** .

## <a name="risky-sign-ins-report-known-issues"></a>Kända problem med riskfyllda inloggnings rapporter

**Vid en** risk identifiering anges statusen till användare som **godkände MFA med en riskfylld princip**.

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Varför kan jag inte ange egna risk nivåer för varje risk identifiering?

Risk nivåer i identitets skydd baseras på precisionen för identifiering och drivs av vår övervakade maskin inlärning. För att anpassa vad som händer med användare kan administratören ta med/undanta vissa användare/grupper från användar risken och inloggnings risk principerna.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Varför matchar inte platsen för en inloggning var användaren verkligen är inloggad?

Mappning av IP-geolokalisering är en utmaning i hela branschen. Om du anser att platsen som anges i inloggnings rapporten inte matchar den faktiska platsen, kontakta Microsoft support. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hur fungerar återkopplings metoderna i identitets skydd?

**Bekräfta komprometterad** (på inloggning) – informerar Azure AD Identity Protection att inloggningen inte utfördes av identitets ägaren och indikerar ett problem.

- När du får den här feedbacken flyttar vi inloggnings-och användar risk tillstånd till **bekräftat komprometterad** och risk nivå till **hög**.

- Dessutom tillhandahåller vi informationen till våra Machine Learning-system för framtida förbättringar av riskbedömning.

    > [!NOTE]
    > Om användaren redan har reparerats klickar du inte på **Bekräfta komprometterad** eftersom den flyttar in inloggnings-och användar risk tillstånd till **bekräftat komprometterad** och risk nivå till **hög**.

**Bekräfta säker** (vid inloggning) – informerar Azure AD Identity Protection att inloggningen har utförts av identitets ägaren och inte tyder på en kompromiss.

- När du får den här feedbacken flyttar vi inloggnings läget (inte användarens) risk tillstånd till **bekräftat säkert** och risk nivån till **-** .

- Dessutom tillhandahåller vi informationen till våra Machine Learning-system för framtida förbättringar av riskbedömning.

    > [!NOTE]
    > Om du tror att användaren inte har komprometterats kan du använda **Ignorera användar risk** på användar nivå i stället för att använda **bekräftad säkerhet** på inloggnings nivå. En avstängnings **risk** på användar nivå stänger användar risken och alla tidigare riskfyllda inloggningar och risk identifieringar.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Varför ser jag en användare med låg (eller högre) risk poäng, även om inga riskfyllda inloggningar eller risk identifieringar visas i Identity Protection?

Med tanke på att användar risken är kumulativ och inte upphör att gälla, kan en användare ha risk för låg eller högre, även om det inte finns några senaste riskfyllda inloggningar eller risk identifieringar som visas i identitets skyddet. Den här situationen kan inträffa om den enda skadliga aktiviteten på en användare ägde rum efter den tidsram för vilken vi lagrar information om riskfyllda inloggningar och risk identifieringar. Vi upphör inte att gälla för användar risken eftersom dåliga aktörer har varit kända i kundernas miljö över 140 dagar bakom en komprometterad identitet innan de ökar sin attack. Kunder kan granska användarens risk tids linje för att ta reda på varför en användare riskerar att gå till: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Varför har en inloggning en "inloggnings risk (agg regering)" på hög nivå när de identifieringar som är associerade med den är av låg eller medelhög risk?

Hög mängd risk poängen kan baseras på andra funktioner i inloggningen eller det faktum att fler än en identifiering har Aktiver ATS för den inloggningen. En inloggning kan dessutom ha en inloggnings risk (agg regering) av medel, även om identifieringarna som är associerade med inloggningen är av hög risk. 
