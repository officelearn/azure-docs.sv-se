---
title: Så här konfigurerar du användar risk principen i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar principen för Azure AD Identity Protection användar risk.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126289"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Instruktioner: Konfigurera riskprincipen för användare

Med användar risken identifierar Azure AD sannolikheten för att ett användar konto har komprometterats. Som administratör kan du konfigurera en användar risk princip för villkorlig åtkomst, så att den automatiskt svarar på en speciell användar risk nivå.
 
Den här artikeln innehåller den information du behöver för att konfigurera en användar risk princip.

## <a name="what-is-a-user-risk-policy"></a>Vad är en användar risk princip?

Azure AD analyserar varje inloggning av en användare. Syftet med analysen är att identifiera misstänkta åtgärder som kommer tillsammans med inloggningen. I Azure AD kan de misstänkta åtgärder som systemet kan identifiera också kallas för risk identifiering. Vissa risk identifieringar kan upptäckas i real tid, men det finns också risk identifieringar som kräver mer tid. Om du till exempel vill upptäcka en omöjlig resa till ovanlig-platser, kräver systemet en inledande inlärnings period på 14 dagar för att lära dig om en användares normala beteende. Det finns flera alternativ för att lösa identifierade risk identifieringar. Du kan till exempel lösa enskilda risk identifieringar manuellt eller så kan du få dem att lösas med hjälp av en inloggnings risk eller en användar risk princip för villkorlig åtkomst.

Alla risk identifieringar som har identifierats för en användare och inte löstes kallas för aktiva risk identifieringar. De aktiva risk identifieringar som är associerade med en användare kallas användar risk. Utifrån användar risken beräknar Azure AD en sannolikhet (låg, medel, hög) som en användare har komprometterat. Sannolikheten kallas användar risk nivå.

![Användar risker](./media/howto-user-risk-policy/1031.png)

Användar risk principen är ett automatiserat svar som du kan konfigurera för en enskild risk nivå för användare. Med en användar risk princip kan du blockera åtkomst till dina resurser eller kräva en lösen ords ändring för att få ett användar konto tillbaka till ett rent tillstånd.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hur gör jag för att åtkomst till användar risk principen?
   
Principen för inloggnings risker finns i avsnittet **Konfigurera** på [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för användare](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Principinställningar

När du konfigurerar inloggnings risk principen måste du ange:

- De användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-user-risk-policy/11.png)

- Den inloggnings risk nivå som utlöser principen:

    ![Användarisknivå](./media/howto-user-risk-policy/12.png)

- Den typ av åtkomst du vill tillämpa när inloggnings risk nivån har uppfyllts:  

    ![Åtkomst](./media/howto-user-risk-policy/13.png)

- Principens tillstånd:

    ![Tillämpa princip](./media/howto-user-risk-policy/14.png)

I dialog rutan princip konfiguration får du ett alternativ för att uppskatta effekten av konfigurationen.

![Uppskattad påverkan](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan ange en säkerhets princip för användar risk för att blockera användare vid inloggning, beroende på risk nivån.

![Blockerar](./media/howto-user-risk-policy/16.png)

Blockerar en inloggning:

* Förhindrar generering av nya användar risk identifieringar för den berörda användaren
* Gör det möjligt för administratörer att manuellt reparera risk identifieringar som påverkar användarens identitet och återställa den till ett säkert tillstånd

## <a name="best-practices"></a>Bästa praxis

Om du väljer ett **högt** tröskelvärde minskar antalet gånger som en princip utlöses och minimerar påverkan på användare.
Men den utesluter **små** och **medel stora** användare som har flaggats för risk från principen, vilket inte kan skydda identiteter eller enheter som tidigare misstänkts eller var kända för att bli komprometterade.

När du anger principen

* Exkludera användare som sannolikt kommer att generera massor av falskt-positiva (utvecklare, säkerhets analyser)
* Exkludera användare i språk där det inte är praktiskt att aktivera principen (till exempel ingen åtkomst till supportavdelningen)
* Använd ett **högt** tröskelvärde under den inledande principen eller om du måste minimera utmaningarna som visas av slutanvändarna.
* Använd ett **lågt** tröskelvärde om organisationen kräver större säkerhet. Om du väljer en **låg** tröskel införs ytterligare användar inloggnings utmaningar, men ökad säkerhet.

Den rekommenderade standarden för de flesta organisationer är att konfigurera en regel för en **medels Tor** tröskel för att få en balans mellan användbarhet och säkerhet.

En översikt över den relaterade användar upplevelsen finns i:

* [Komprometterat konto återställnings flöde](flows.md#compromised-account-recovery).  
* [Komprometterat konto blockerat flöde](flows.md#compromised-account-blocked).  

**Så här öppnar du dialog rutan relaterad konfiguration**:

- Klicka på **användar risk princip**i avsnittet **Konfigurera** på bladet **Azure AD Identity Protection** .

    ![Användar risk princip](./media/howto-user-risk-policy/1009.png "Användar risk princip")

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview.md).
