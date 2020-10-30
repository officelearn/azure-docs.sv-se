---
title: Vanliga frågor och svar om identitets skydd i Azure Active Directory
description: Vanliga frågor och svar Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8fdabc3f4f33627936eead9dda57e67c7b0da8
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040505"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Vanliga frågor om identitets skydd i Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorera kända problem med användar risker

**Ignorera användar risk** i klassiskt identitets skydd ställer in aktören i användarens risk historik i identitets skydd till **Azure AD** .

**Ignorera användar risk** i identitets skydd anger aktören i användarens risk historik i identitets skydd till **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Det finns ett aktuellt känt problem som orsakar svars tider i det avstängda användar risk flödet. Om du har en "användar risk princip" slutar den här principen att gälla för avstängda användare i minuter att klicka på "Stäng användar risk". Det finns dock kända fördröjningar med UXen som uppdaterar "risk tillstånd" för avstängda användare. Som en lösning kan du uppdatera sidan på webb läsar nivån för att se den senaste användaren "risk status".

## <a name="risky-users-report-known-issues"></a>Riskfyllda användare rapportera kända problem

Frågor i fältet **username** är Skift läges känsliga, medan frågor i fältet **namn** är Case-oberoende.

Om du omväxlar **visas datum som** döljer den **senaste uppdaterade** kolumnen. Om du vill läsa kolumnen klickar du på **kolumner** överst på bladet riskfyllda användare.

**Ignorera alla händelser** i klassiskt identitets skydd anger status för risk identifieringar till **stängda (löst)** .

## <a name="risky-sign-ins-report-known-issues"></a>Kända problem med riskfyllda inloggnings rapporter

**Vid en** risk identifiering anges statusen till användare som **godkände MFA med en riskfylld princip** .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-a-user-is-at-risk"></a>Varför är en användare utsatt för risk?

Om du är Azure AD Identity Protection kund går du till vyn [riskfyllda användare](howto-identity-protection-investigate-risk.md#risky-users) och klickar på en användare som är utsatt för risk. I kassan längst ned visas alla händelser som ledde till en ändring av användar risken i fliken risk historik. Om du vill se alla riskfyllda inloggningar för användaren klickar du på användarens riskfyllda inloggningar. Om du vill se alla risk identifieringar för den här användaren klickar du på användarens risk identifieringar.

## <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Varför har min inloggning blockerats men identitets skydd genererade inte någon risk identifiering?
Inloggningar kan blockeras av flera orsaker. Det är viktigt att Observera att identitets skydd endast genererar risk identifiering när rätt autentiseringsuppgifter används i autentiseringsbegäran. Om en användare använder felaktiga autentiseringsuppgifter, kommer den inte att flaggas av identitets skydd eftersom det inte finns risk för komprometterande autentiseringsuppgifter om inte en felaktig aktör använder rätt autentiseringsuppgifter. Vissa orsaker till att en användare kan blockeras från signering som inte genererar en identifiering av identitets skydd är:
* **IP-adressen kan ha blockerats** på grund av skadlig aktivitet från IP-adressen. Det blockerade IP-meddelandet särskiljer inte om autentiseringsuppgifterna var korrekta eller inte. Om IP-adressen är blockerad och rätt autentiseringsuppgifter inte används genererar den ingen identifiering av identitets skydd
* **[Smart utelåsning](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)** kan blockera kontot från att logga in efter flera misslyckade försök
* En **princip för villkorlig åtkomst** kan tillämpas som använder andra villkor än risk nivån för att blockera en autentiseringsbegäran

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hur kan jag få en rapport om identifieringar av en speciell typ?

Gå till vyn risk identifieringar och filtrera efter identifierings typ. Sedan kan du ladda ned den här rapporten i. CSV eller. JSON-format med knappen **Hämta** överst. Mer information finns i artikeln [How to: Undersök risker](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Varför kan jag inte ange egna risk nivåer för varje risk identifiering?

Risk nivåer i identitets skydd baseras på precisionen för identifiering och drivs av vår övervakade maskin inlärning. För att anpassa vad som händer med användare kan administratören ta med/undanta vissa användare/grupper från användar risken och Sign-In risk principer.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Varför matchar inte platsen för en inloggning var användaren verkligen är inloggad?

Mappning av IP-geolokalisering är en utmaning i hela branschen. Om du anser att platsen som anges i inloggnings rapporten inte matchar den faktiska platsen, kontakta Microsoft support. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hur kan jag stänga vissa risk identifieringar som jag gjorde i det gamla användar gränssnittet?

Du kan ge feedback om risk identifieringar genom att bekräfta att den länkade inloggningen är komprometterad eller säker. Den feedback som ges på inloggnings tricklesen till alla identifieringar som görs vid inloggningen. Om du vill stänga identifieringar som inte är länkade till en inloggning kan du ange den feedback på användar nivå. Mer information finns i artikeln [How to: ge risk feedback i Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Hur långt kan jag gå tillbaka i tid för att förstå vad som händer med min användare?

- I vyn [riskfyllda användare](howto-identity-protection-investigate-risk.md#risky-users) visas en användares risk på grund av alla tidigare inloggningar. 
- I vyn [riskfyllda inloggningar](howto-identity-protection-investigate-risk.md#risky-sign-ins) visas vid risk signaler under de senaste 30 dagarna. 
- Vyn [risk identifieringar](howto-identity-protection-investigate-risk.md#risk-detections) visar risk identifieringar som gjorts under de senaste 90 dagarna.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hur kan jag läsa mer om en speciell identifiering?

Alla risk identifieringar dokumenteras i artikeln [Vad är en risk](concept-identity-protection-risks.md#risk-types-and-detection). Du kan hovra över symbolen (i) bredvid identifieringen på Azure Portal för att lära dig mer om en identifiering.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hur fungerar återkopplings metoderna i identitets skydd?

**Bekräfta komprometterad** (på inloggning) – informerar Azure AD Identity Protection att inloggningen inte utfördes av identitets ägaren och indikerar ett problem.

- När du får den här feedbacken flyttar vi inloggnings-och användar risk tillstånd till **bekräftat komprometterad** och risk nivå till **hög** .

- Dessutom tillhandahåller vi informationen till våra Machine Learning-system för framtida förbättringar av riskbedömning.

    > [!NOTE]
    > Om användaren redan har reparerats klickar du inte på **Bekräfta komprometterad** eftersom den flyttar in inloggnings-och användar risk tillstånd till **bekräftat komprometterad** och risk nivå till **hög** .

**Bekräfta säker** (vid inloggning) – informerar Azure AD Identity Protection att inloggningen har utförts av identitets ägaren och inte tyder på en kompromiss.

- När du får den här feedbacken flyttar vi inloggnings läget (inte användarens) risk tillstånd till **bekräftat säkert** och risk nivån till **-** .

- Dessutom tillhandahåller vi informationen till våra Machine Learning-system för framtida förbättringar av riskbedömning.

    > [!NOTE]
    > Om du tror att användaren inte har komprometterats kan du använda **Ignorera användar risk** på användar nivå i stället för att använda **bekräftad säkerhet** på inloggnings nivå. En avstängnings **risk** på användar nivå stänger användar risken och alla tidigare riskfyllda inloggningar och risk identifieringar.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Varför ser jag en användare med låg (eller högre) risk poäng, även om inga riskfyllda inloggningar eller risk identifieringar visas i Identity Protection?

Med tanke på att användar risken är kumulativ och inte upphör att gälla, kan en användare ha risk för låg eller högre, även om det inte finns några senaste riskfyllda inloggningar eller risk identifieringar som visas i identitets skyddet. Den här situationen kan inträffa om den enda skadliga aktiviteten på en användare ägde rum efter den tidsram för vilken vi lagrar information om riskfyllda inloggningar och risk identifieringar. Vi upphör inte att gälla för användar risken eftersom dåliga aktörer har varit kända i kundernas miljö över 140 dagar bakom en komprometterad identitet innan de ökar sin attack. Kunder kan granska användarens risk tids linje för att ta reda på varför en användare riskerar att gå till: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Varför har en inloggning en "inloggnings risk (agg regering)" på hög nivå när de identifieringar som är associerade med den är av låg eller medelhög risk?

Hög mängd risk poängen kan baseras på andra funktioner i inloggningen eller det faktum att fler än en identifiering har Aktiver ATS för den inloggningen. En inloggning kan dessutom ha en inloggnings risk (agg regering) av medel, även om identifieringarna som är associerade med inloggningen är av hög risk. 

### <a name="why-is-the-detection-which-is-linked-to-a-risky-sign-in-have-a-different-risk-level-than-the-sign-in-risk-level-real-time"></a>Varför har identifieringen som är kopplad till en riskfylld inloggning en annan risk nivå än inloggnings risk nivån (real tid)? 

Vi har nyligen gjort förbättringar för hur real tids inloggnings risken beräknas. Den avvikelse som observeras mellan risk identifierings nivån och inloggnings risk nivån är resultatet av dessa ändringar. Observera att inloggnings risken i real tid är det värde som används vid princip tillämpning. 
