---
title: Vanliga frågor om identitetsskydd i Azure Active Directory
description: Vanliga frågor och svar Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443577"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Vanliga frågor och svar Identitetsskydd i Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Avvisa kända problem med användarrisk

**Avvisa användarrisk** i klassiskt identitetsskydd anger aktören i användarens riskhistorik i Identitetsskydd till **Azure AD**.

**Avvisa användarrisk** i Identity Protection anger aktören i användarens riskhistorik i Identity Protection till ** \<Admin namn\>med en hyperlänk som pekar på användarens blad**.

Det finns ett aktuellt känt problem som orsakar svarstid i användarrisk uppsägning flödet. Om du har en "Användarriskprincip" slutar den här principen att gälla för avvisade användare inom några minuter efter att du klickar på "Avvisa användarrisk". Det finns dock kända fördröjningar med UX uppdatera "Risk tillstånd" av avvisade användare. Som en lösning uppdaterar du sidan på webbläsarnivå för att se den senaste användaren "Risktillstånd".

## <a name="risky-users-report-known-issues"></a>Riskafyllt användare rapporterar kända problem

Frågor i **fältet användarnamn** är skiftlägeskänsliga, medan frågor i fältet **Namn** är skiftlägesaberoende.

Växla **Visa datum som** döljer kolumnen RISK SENAST **UPPDATERAD.** Om du vill läsa kolumnen klickar du på **Kolumner** högst upp i bladet Riskfyllda användare.

**Stäng alla händelser** i klassiskt identitetsskydd anger status för riskidentifieringarna till **Stängd (löst).**

## <a name="risky-sign-ins-report-known-issues"></a>Riskfyllda inloggningar rapporterar kända problem

**Lös** på en riskidentifiering anger status till **användare som har skickats MFA som drivs av riskbaserad princip**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-is-a-user-is-at-risk"></a>Varför är en användare i riskzonen?

Om du är en Azure AD Identity Protection-kund går du till den [riskfyllda användarvyn](howto-identity-protection-investigate-risk.md#risky-users) och klickar på en riskanvändare. I lådan längst ned visar fliken "Riskhistorik" alla händelser som ledde till en användarriskändring. Om du vill se alla riskfyllda inloggningar för användaren klickar du på "Användarens riskfyllda inloggningar". Om du vill se alla riskidentifieringar för den här användaren klickar du på "Användarens riskidentifiering".

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Hur kan jag få en rapport om identifieringar av en viss typ?

Gå till vyn riskidentifiering och filtrera efter identifieringstyp. Du kan sedan hämta den här rapporten i . CSV eller . JSON-format med hjälp av **knappen Ladda ned** högst upp. Mer information finns i artikeln [Så här: Undersök risk](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Varför kan jag inte ställa in mina egna risknivåer för varje riskidentifiering?

Risknivåerna i Identity Protection baseras på precisionen i detektionen och drivs av vår övervakade maskininlärning. Om du vill anpassa vilken upplevelse användare presenteras kan administratören inkludera/utesluta vissa användare/grupper från användarrisk- och inloggningsriskpolicyerna.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Varför stämmer inte platsen för en inloggning där användaren verkligen loggade in från?

IP-geolokaliseringskartläggning är en branschomfattande utmaning. Om du anser att platsen i inloggningsrapporten inte matchar den faktiska platsen kan du kontakta Microsoft-supporten. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Hur stänger jag specifika riskidentifieringar som jag gjorde i det gamla användargränssnittet?

Du kan ge feedback om riskidentifieringar genom att bekräfta den länkade inloggningen som komprometterat eller säkert. Den feedback som ges på inloggningen sipprar ner till alla upptäckter som gjorts på den inloggningen. Om du vill stänga identifieringar som inte är länkade till en inloggning kan du ge den feedbacken på användarnivå. Mer information finns i artikeln [Så här ger du riskfeedback i Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Hur långt kan jag gå tillbaka i tiden för att förstå vad som händer med min användare?

- Den [riskfyllda användarvyn](howto-identity-protection-investigate-risk.md#risky-users) visar en användares riskstatus baserat på alla tidigare inloggningar. 
- Den [riskfyllda inloggningsvyn](howto-identity-protection-investigate-risk.md#risky-sign-ins) visar risktecken under de senaste 30 dagarna. 
- Vyn [riskidentifiering](howto-identity-protection-investigate-risk.md#risk-detections) visar riskidentifieringar som gjorts under de senaste 90 dagarna.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Hur kan jag läsa mer om en specifik identifiering?

Alla riskidentifieringar dokumenteras i artikeln [Vad är risk](concept-identity-protection-risks.md#risk-types-and-detection). Du kan hovra över (i)-symbolen bredvid identifieringen på Azure-portalen om du vill veta mer om en identifiering.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hur fungerar återkopplingsmekanismerna i Identity Protection?

**Bekräfta komprometterade** (vid inloggning) – Informerar Azure AD Identity Protection om att inloggningen inte utfördes av identitetsägaren och anger en kompromiss.

- När vi får den här feedbacken flyttar vi inloggnings- och användarrisktillståndet till **Bekräftad komprometterad** och risknivå till **Hög**.

- Dessutom tillhandahåller vi informationen till våra maskininlärningssystem för framtida förbättringar av riskbedömningen.

    > [!NOTE]
    > Om användaren redan har åtgärdats klickar du inte på **Bekräfta komprometterad** eftersom den flyttar inloggnings- och användarrisktillståndet till **Bekräftad komprometterad** och risknivån till **Hög**.

**Bekräfta säker** (vid inloggning) – Informerar Azure AD Identity Protection om att inloggningen utfördes av identitetsägaren och inte anger någon kompromiss.

- När du får den här feedbacken flyttar vi inloggningsrisktillståndet (inte **-** användaren) till Bekräftat **säkert** och risknivån till .

- Dessutom tillhandahåller vi informationen till våra maskininlärningssystem för framtida förbättringar av riskbedömningen.

    > [!NOTE]
    > Om du tror att användaren inte äventyras använder du **Avvisa användarrisk** på användarnivå i stället för att använda **Bekräftat säkert** på inloggningsnivå. En **Ignorera användarrisk** på användarnivå stänger användarrisken och alla tidigare riskfyllda inloggningar och riskidentifieringar.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Varför ser jag en användare med en låg (eller högre) riskpoäng, även om inga riskfyllda inloggningar eller riskidentifieringar visas i Identity Protection?

Med tanke på att användarrisken är kumulativ till sin natur och inte upphör att gälla kan en användare ha en användarrisk för låg eller högre, även om det inte finns några aktuella riskfyllda inloggningar eller riskidentifieringar som visas i Identity Protection. Den här situationen kan inträffa om den enda skadliga aktiviteten för en användare ägde rum utöver den tidsram för vilken vi lagrar information om riskfyllda inloggningar och riskidentifieringar. Vi upphör inte att använda användarrisk eftersom dåliga aktörer har varit kända för att stanna i kundernas miljö över 140 dagar bakom en komprometterade identitet innan ramp upp sin attack. Kunder kan granska användarens risktidslinje för att förstå varför en användare är i riskzonen genom att gå till:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Varför har en inloggning en "inloggningsrisk (aggregerad)" poäng för Hög när de identifieringar som är associerade med den är av låg eller medelhög risk?

Den höga sammanlagda riskpoängen kan baseras på andra funktioner i inloggningen, eller det faktum att mer än en identifiering avfyrades för inloggningen. Omvänt kan en inloggning ha en inloggningsrisk (aggregerad) av Medium även om de identifieringar som är associerade med inloggningen är av hög risk. 
