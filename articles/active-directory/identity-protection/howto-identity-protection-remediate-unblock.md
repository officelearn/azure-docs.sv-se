---
title: Åtgärda risker och avblockera användare i Azure AD Identity Protection
description: Läs mer om de alternativ du har nära aktiva riskidentifieringar.
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
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382107"
---
# <a name="remediate-risks-and-unblock-users"></a>Åtgärda risker och avblockera användare

När du har slutfört [undersökningen](howto-identity-protection-investigate-risk.md)vill du vidta åtgärder för att åtgärda risken eller häva blockeringen av användare. Organisationer har också möjlighet att aktivera automatisk reparation med hjälp av sina [riskpolicyer](howto-identity-protection-configure-risk-policies.md). Organisationer bör försöka stänga alla riskidentifieringar som de presenteras med under en tidsperiod som organisationen är bekväm med. Microsoft rekommenderar att du stänger händelser så snart som möjligt eftersom tiden är viktig när du arbetar med risk.

## <a name="remediation"></a>Åtgärder

Alla aktiva riskidentifieringar bidrar till beräkningen av ett värde som kallas användarrisknivå. Användarrisknivån är en indikator (låg, medelhög, hög) för sannolikheten för att ett konto har komprometterats. Som administratör vill du få alla riskidentifieringar stängda, så att de berörda användarna inte längre är i riskzonen.

Vissa riskidentifieringar kan markeras av Identity Protection som "Closed (system)" eftersom händelserna inte längre bedömdes vara riskfyllda.

Administratörer har följande alternativ för att åtgärda:

- Självsanering med riskpolicy
- Manuell återställning av lösenord
- Avvisa användarrisk
- Stäng enskilda riskidentifieringar manuellt

### <a name="self-remediation-with-risk-policy"></a>Självsanering med riskpolicy

Om du tillåter användare att själv åtgärda, med Azure Multi-Factor Authentication (MFA) och självbetjäningslösenordsåterställning (SSPR) i dina riskprinciper, kan de avblockera sig när risken upptäcks. Dessa identifieringar anses sedan vara stängda. Användare måste ha registrerats tidigare för Azure MFA och SSPR för att kunna använda när risken upptäcks.

Vissa identifieringar kan inte öka risken till den nivå där en användare självreparation skulle krävas, men administratörer bör fortfarande utvärdera dessa identifieringar. Administratörer kan avgöra att ytterligare åtgärder är nödvändiga som [att blockera åtkomst från platser](../conditional-access/howto-conditional-access-policy-location.md) eller sänka den acceptabla risken i sina principer.

### <a name="manual-password-reset"></a>Manuell återställning av lösenord

Om det inte är ett alternativ att kräva en återställning av lösenord med hjälp av en användarriskprincip kan administratörer stänga alla riskidentifieringar för en användare med manuell återställning av lösenord.

Administratörer får två alternativ när de återställer ett lösenord för sina användare:

- **Generera ett tillfälligt lösenord** - Genom att generera ett tillfälligt lösenord kan du omedelbart föra en identitet tillbaka till ett säkert tillstånd. Den här metoden kräver att du kontaktar de berörda användarna eftersom de behöver veta vad det tillfälliga lösenordet är. Eftersom lösenordet är tillfälligt uppmanas användaren att ändra lösenordet till något nytt under nästa inloggning.

- **Kräv att användaren återställer lösenord** - Kräver att användarna återställer lösenord möjliggör självåterställning utan att kontakta helpdesk eller administratör. Den här metoden gäller endast användare som är registrerade för Azure MFA och SSPR. För användare som inte har registrerats är det här alternativet inte tillgängligt.

### <a name="dismiss-user-risk"></a>Avvisa användarrisk

Om en återställning av lösenord inte är ett alternativ för dig, eftersom användaren till exempel har tagits bort, kan du välja att avvisa användarriskidentifiering.

När du klickar på **Avvisa användarrisk**stängs alla händelser och den berörda användaren är inte längre i riskzonen. Men eftersom den här metoden inte påverkar det befintliga lösenordet, förs inte den relaterade identiteten tillbaka till ett säkert tillstånd. 

### <a name="close-individual-risk-detections-manually"></a>Stäng enskilda riskidentifieringar manuellt

Du kan stänga enskilda riskidentifieringar manuellt. Genom att stänga riskidentifieringar manuellt kan du sänka användarrisknivån. Vanligtvis stängs riskidentifieringar manuellt som svar på en relaterad undersökning. När du till exempel pratar med en användare visas att en aktiv riskidentifiering inte längre krävs. 
 
När du stänger riskidentifieringar manuellt kan du välja att vidta någon av följande åtgärder för att ändra status för en riskidentifiering:

- Bekräfta att användaren komprometterats
- Avvisa användarrisk
- Bekräfta inloggningen säker
- Bekräfta komprometterade inloggningar

## <a name="unblocking-users"></a>Avblockera användare

En administratör kan välja att blockera en inloggning baserat på deras riskpolicy eller utredningar. Ett block kan uppstå baserat på antingen inloggning eller användarrisk.

### <a name="unblocking-based-on-user-risk"></a>Avblockera baserat på användarrisk

Om du vill häva blockeringen av ett konto som blockerats på grund av användarrisk har administratörer följande alternativ:

1. **Återställ lösenord** - Du kan återställa användarens lösenord.
1. **Avvisa användarrisk** - Användarriskprincipen blockerar en användare om den konfigurerade användarrisknivån för blockering av åtkomst har uppnåtts. Du kan minska en användares risknivå genom att avvisa användarrisk eller manuellt stänga rapporterade riskidentifieringar.
1. **Uteslut användaren från principen** - Om du tror att den aktuella konfigurationen av inloggningsprincipen orsakar problem för specifika användare kan du utesluta användarna från den. Mer information finns i avsnittet Undantag i artikeln Så här konfigurerar och aktiverar du [riskprinciper](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Inaktivera princip** – Om du tror att principkonfigurationen orsakar problem för alla användare kan du inaktivera principen. Mer information finns i artikeln [Så här konfigurerar och aktiverar du riskprinciper](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Avblockera baserat på inloggningsrisk

Om du vill ta bort blockeringen av ett konto baserat på inloggningsrisk har administratörer följande alternativ:

1. **Logga in från en välbekant plats eller enhet** – En vanlig orsak till blockerade misstänkta inloggningar är inloggningsförsök från okända platser eller enheter. Användarna kan snabbt avgöra om den här orsaken är blockeringsorsaken genom att försöka logga in från en välbekant plats eller enhet.
1. **Uteslut användaren från principen** - Om du tror att den aktuella konfigurationen av inloggningsprincipen orsakar problem för specifika användare kan du utesluta användarna från den. Mer information finns i avsnittet Undantag i artikeln Så här konfigurerar och aktiverar du [riskprinciper](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Inaktivera princip** – Om du tror att principkonfigurationen orsakar problem för alla användare kan du inaktivera principen. Mer information finns i artikeln [Så här konfigurerar och aktiverar du riskprinciper](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du får en översikt över Azure AD Identity Protection finns i [översikten över Azure AD Identity Protection](overview-identity-protection.md).
