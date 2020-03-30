---
title: Ge riskfeedback i Azure Active Directory Identity Protection
description: Hur och varför ska du ge feedback om identitetsskydd riskidentifieringar.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382081"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Så här ger du riskfeedback i Azure AD Identity Protection

Med Azure AD Identity Protection kan du ge feedback om riskbedömningen. I följande dokument visas de scenarier där du vill ge feedback om Azure AD Identity Protections riskbedömning och hur vi införlivar den.

## <a name="what-is-a-detection"></a>Vad är en upptäckt?

Identifiering av identitetsskydd är en indikator på misstänkt aktivitet ur ett identitetsriskperspektiv. Dessa misstänkta aktiviteter kallas riskidentifiering. Dessa identitetsbaserade identifieringar kan baseras på heuristik, maskininlärning eller kan komma från partnerprodukter. Dessa identifieringar används för att fastställa inloggningsrisk och användarrisk,

* Användarrisken representerar sannolikheten för att en identitet äventyras.
* Inloggningsrisken representerar sannolikheten för att en inloggning äventyras (till exempel är inloggningen inte auktoriserad av identitetsägaren).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Varför ska jag ge riskfeedback till Azure AD:s riskbedömningar? 

Det finns flera anledningar till varför du bör ge Azure AD-riskfeedback:

- **Azure AD:s användare eller inloggningsriskbedömning felaktig**. En inloggning som visas i rapporten Risky-inloggningar var till exempel godartad och alla identifieringar på inloggningen var falska positiva.
- **Du har validerat att Azure AD:s riskbedömning för användare eller inloggning var korrekt**. Till exempel var en inloggning som visas i rapporten Risky inloggningar verkligen skadlig och du vill att Azure AD ska veta att alla identifieringar på inloggningen var sanna positiva.
- **Du åtgärdade risken för den användaren utanför Azure AD Identity Protection** och du vill att användarens risknivå ska uppdateras.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Hur använder Azure AD min riskfeedback?

Azure AD använder din feedback för att uppdatera risken för den underliggande användaren och/eller inloggningen och riktigheten av dessa händelser. Den här feedbacken hjälper till att skydda slutanvändaren. När du till exempel har bekräftat att en inloggning har komprometterats ökar Azure AD omedelbart användarens risk och inloggningens sammanlagda risk (inte realtidsrisk) till Hög. Om den här användaren ingår i användarriskpolicyn för att tvinga högriskanvändare att återställa sina lösenord på ett säkert sätt, kommer användaren automatiskt att åtgärda sig själv nästa gång de loggar in.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Hur ska jag ge riskåterkoppling och vad som händer under huven?

Här är scenarierna och mekanismerna för att ge riskfeedback till Azure AD.

| Scenario | Hur man ger feedback? | Vad händer under huven? | Anteckningar |
| --- | --- | --- | --- |
| **Inloggningen har inte komprometterats (falskt positivt)** <br> Rapporten "Riskfyllda inloggningar" visar en inloggning i riskzonen [Risktillstånd = I riskzonen] men att inloggningen inte komprometterats. | Välj inloggning och klicka på "Bekräfta inloggningssäkert". | Azure AD flyttar inloggningens sammanlagda risk till ingen [Risktillstånd = Bekräftat säkert; Risknivå (Aggregat) = -] och kommer att vända dess inverkan på användarrisken. | För närvarande är alternativet Bekräfta inloggningssäker endast tillgängligt i rapporten Risky-inloggningar. |
| **Inloggning komprometterat (Sant positivt)** <br> "Riskfyllda inloggningar" visar en inloggning i riskzonen [Risktillstånd = I riskzonen] med låg risk [Risknivå (Aggregat) = Låg] och att inloggning verkligen komprometterades. | Välj inloggningen och klicka på "Bekräfta inloggning komprometterat". | Azure AD flyttar inloggningens samlade risk och användarrisken till Hög [Risktillstånd = Bekräftad komprometterad; Risknivå = Hög]. | För närvarande är alternativet Bekräfta inloggning komprometterade endast tillgängligt i rapporten Risky-inloggningar. |
| **Användaren komprometterade (Sant positivt)** <br> Rapporten "Risky users" visar en riskanvändare [Risktillstånd = I riskzonen] med låg risk [Risknivå = Låg] och den användaren har verkligen komprometterats. | Välj användaren och klicka på "Bekräfta att användaren komprometterats". | Azure AD flyttar användarrisken till högt [risktillstånd = Bekräftat komprometterat; Risknivå = Hög] och kommer att lägga till en ny identifiering "Admin bekräftade användaren äventyras". | För närvarande är alternativet Bekräfta komprometterade användare endast tillgängligt i rapporten Risky users. <br> Identifieringen "Admin bekräftade användare komprometterade" visas på fliken "Riskidentifieringar som inte är länkade till en inloggning" i rapporten Risky users. |
| **Användaren åtgärdas utanför Azure AD Identity Protection (Sant positivt + Åtgärdas)** <br> Rapporten "Riskfyllda användare" visar en användare i riskzonen och jag har därefter åtgärdat användaren utanför Azure AD Identity Protection. | 1. Välj användaren och klicka på "Bekräfta att användaren komprometterats". (Den här processen bekräftar för Azure AD att användaren verkligen komprometterades.) <br> 2. Vänta tills användarens "Risknivå" går till Hög. (Den här gången ger Azure AD den tid som behövs för att ta ovanstående feedback till riskmotorn.) <br> 3. Välj användaren och klicka på "Avvisa användarrisk". (Den här processen bekräftar för Azure AD att användaren inte längre har komprometterats.) |  Azure AD flyttar användarrisken till ingen [Risktillstånd = Avvisad; Risknivå = -] och stänger risken för alla befintliga inloggningar med aktiv risk. | Om du klickar på "Avvisa användarrisk" stänger du alla risker för användaren och tidigare inloggningar. Det går inte att ångra den här åtgärden. |
| **Användaren har inte komprometterats (falskt positivt)** <br> Rapporten "Riskfyllda användare" visas hos användaren i riskzonen, men användaren är inte komprometterat. | Markera användaren och klicka på "Avvisa användarrisk". (Den här processen bekräftar för Azure AD att användaren inte har komprometterats.) | Azure AD flyttar användarrisken till ingen [Risktillstånd = Avvisad; Risknivå = -]. | Om du klickar på "Avvisa användarrisk" stänger du alla risker för användaren och tidigare inloggningar. Det går inte att ångra den här åtgärden. |
| Jag vill stänga användarrisken men jag är inte säker på om användaren är äventyras / säker. | Markera användaren och klicka på "Avvisa användarrisk". (Den här processen bekräftar för Azure AD att användaren inte längre har komprometterats.) | Azure AD flyttar användarrisken till ingen [Risktillstånd = Avvisad; Risknivå = -]. | Om du klickar på "Avvisa användarrisk" stänger du alla risker för användaren och tidigare inloggningar. Det går inte att ångra den här åtgärden. Vi rekommenderar att du åtgärdar användaren genom att klicka på "Återställ lösenord" eller be användaren att återställa/ändra sina autentiseringsuppgifter på ett säkert sätt. |

Feedback på användarriskidentifieringar i Identitetsskydd bearbetas offline och kan ta lite tid att uppdatera. Kolumnen riskbearbetningstillstånd ger det aktuella tillståndet för feedbackbearbetning.

![Riskbehandlingstillstånd för riskfylld användarrapport](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory Identity Protection-riskidentifieringsreferens](risk-events-reference.md)
