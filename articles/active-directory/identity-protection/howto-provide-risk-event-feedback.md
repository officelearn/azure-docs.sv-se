---
title: Ge feedback på riskhändelser i Azure AD Identity Protection - Azure Active Directory
description: Hur och varför ska du ge feedback på Identity Protection riskhändelser.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827912"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Instruktioner: Ge feedback för risk i Azure AD Identity Protection

Azure AD Identity Protection kan du ge feedback om dess riskbedömning. Följande dokument visar de scenarier där du vill ge feedback om Azure AD Identity Protection riskbedömning och hur vi lägga till den.

## <a name="what-is-a-detection"></a>Vad är en identifiering?

En Identity Protection-identifiering är ett tecken på misstänkt aktivitet från en identitet risk perspektiv. Dessa aktiviteter kallas riskhändelser. Dessa identitetsbaserade identifieringar kan baseras på heuristik, machine learning eller kan komma från partnerprodukter. Dessa identifieringar som används för att fastställa inloggningsrisk och användarrisk,

* Användarrisk representerar sannolikheten för en identitet har komprometterats.
* Inloggningsrisk representerar sannolikheten för en inloggning komprometteras (till exempel inloggningen har inte behörighet av ägaren identitet).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Varför ska jag ge risk feedback till Azure Active Directorys riskbedömningar? 

Det finns flera orsaker till varför du bör ge feedback för Azure AD-risk:

1. **Du hittar Azure AD-användare eller logga in riskbedömning felaktig**. Till exempel en inloggning ”riskfyllda inloggningar” rapporten var ofarliga och alla identifieringar om att inloggningen har falska positiva identifieringar.
1. **Du har verifierat den Azure AD-användare eller logga in riskbedömning var korrekt**. Till exempel en inloggning ”riskfyllda inloggningar” rapporten har verkligen skadlig och du vill att Azure AD att veta att alla identifieringar om att inloggningen har korrekta positiva identifieringar.
1. **Du reparerade risken på användaren utanför Azure AD Identity Protection** och du vill att användarens risknivå som ska uppdateras.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hur används min feedback för risk i Azure AD?

Azure AD använder din feedback för att uppdatera risken för den underliggande användare och/eller logga in. Denna feedback kan du skydda slutanvändaren. När du har bekräftat en inloggning komprometteras ökar till exempel Azure AD direkt egen risk och sammanställd inloggningsrisk (inte i realtid risker) till hög. Om den här användaren ingår i din princip för användarrisk att tvinga högriskanvändare kan på ett säkert sätt återställa sina lösenord, ska användaren ändras automatiskt själva nästa gång de loggar in.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hur ska jag ge risk feedback och vad som händer under huven?

Här följer de scenarier och mekanismer för att ge risk feedback till Azure AD.

| Scenario | Så här att ge feedback? | Vad händer under huven? | Anteckningar |
| --- | --- | --- | --- |
| **Logga in inte komprometteras (falskt positivt resultat)** <br> 'Riskfyllda inloggningar ”rapporten innehåller en Projektstatistik inloggning [riskerar tillstånd = risk] utan att inloggning inte har komprometterats. | Välj inloggningen och klicka på Bekräfta logga in säkert. | Azure AD kommer att flytta den sammanställd inloggningsrisk NONE [riskerar tillstånd = bekräftat säkra. Risknivå (aggregering) =-] och återför dess inverkan på användare risk för angrepp. | För närvarande finns bara alternativet Bekräfta inloggning safe i ”riskfyllda inloggningar” rapporten. |
| **Logga in komprometterats (sann positiv)** <br> 'Riskfyllda inloggningar ”rapporten innehåller en Projektstatistik inloggning [riskerar tillstånd = risk] med låg risk [risknivå (aggregering) = låg] och den inloggningen verkligen har komprometterats. | Välj inloggningen och klicka på ”Bekräfta inloggning komprometteras'. | Flyttas den sammanställd inloggningsrisk och risken för användaren i Azure AD till hög [riskerar tillstånd = bekräftat komprometteras; Risknivå = hög]. | För närvarande finns bara alternativet ”Bekräfta inloggning komprometteras' i 'Riskfyllda inloggningar” rapporten. |
| **Användaren har komprometterats (sann positiv)** <br> ”Riskfylld användare”-rapporten innehåller en Projektstatistik användare [riskerar tillstånd = risk] med låg risk [risknivå = låg] och användaren verkligen har komprometterats. | Välj användaren och klicka på ”Bekräfta användare komprometteras”. | Azure AD kommer att flytta användarrisk till hög [riskerar tillstånd = bekräftat komprometteras; Risknivå = hög] och lägger till en ny identifiering ”Admin bekräftat användaren komprometteras'. | För närvarande finns bara alternativet ”Bekräfta användare komprometteras” i ”riskfylld användare”-rapport. <br> Identifieringen 'Admin bekräftat användaren komprometteras ”visas på fliken” riskhändelser som inte har länkats till en inloggning ”i rapporten” riskfylld användare ”. |
| **Användaren repareras utanför Azure AD Identity Protection (sann positiv händelse + Remediated)** <br> ”Riskfylld användare”-rapporten innehåller en Projektstatistik användare och jag därefter har reparerats användare utanför Azure AD Identity Protection. | 1. Välj användaren och klicka på ”Bekräfta användare komprometteras”. (Den här processen bekräftar till Azure AD att användaren verkligen har komprometterats.) <br> 2. Vänta tills användaren 'risknivå ”gå till hög. (Den här gången ger Azure AD tid som behövs för att ta ovan feedback till risk-motorn.) <br> 3. Välj användaren och klicka på ”Stäng användarrisk'. (Den här processen bekräftar till Azure AD att användaren inte längre komprometteras.) |  Azure AD flyttar användarrisk NONE [riskerar tillstånd = avvisat; Risknivå =-] och stänger risken på alla befintliga inloggningar med aktiv risk. | Klicka på ”Stäng användarrisk' stängs alla risker på användaren och de senaste inloggningar. Den här åtgärden kan inte ångras. |
| **Användare som inte komprometteras (falskt positivt resultat)** <br> ”Riskfylld användare”-rapport som visar på vilka användare men användaren komprometteras inte. | Välj användaren och klicka på ”Stäng användarrisk'. (Den här processen bekräftar till Azure AD att användaren inte komprometteras.) | Azure AD flyttar användarrisk NONE [riskerar tillstånd = avvisat; Risknivå =-]. | Klicka på ”Stäng användarrisk' stängs alla risker på användaren och de senaste inloggningar. Den här åtgärden kan inte ångras. |
| Jag vill du stänga användarrisk men jag vet inte om användaren är komprometterade / safe. | Välj användaren och klicka på ”Stäng användarrisk'. (Den här processen bekräftar till Azure AD att användaren inte längre komprometteras.) | Azure AD flyttar användarrisk NONE [riskerar tillstånd = avvisat; Risknivå =-]. | Klicka på ”Stäng användarrisk' stängs alla risker på användaren och de senaste inloggningar. Den här åtgärden kan inte ångras. Vi rekommenderar att du reparera användaren genom att klicka på ”Återställ lösenord” eller be användaren att på ett säkert sätt återställning/ändra sina autentiseringsuppgifter. |

Feedback om användarriskhändelser på Identity Protection har bearbetats offline och kan ta lite tid att uppdatera. Risken bearbetning delstats-kolumnen ger det aktuella tillståndet för bearbetning av feedback.

![Riskerar Bearbetningsstatus för riskfyllda rapport](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

[Referera till Azure Active Directory Identity Protection riskhändelser](risk-events-reference.md)