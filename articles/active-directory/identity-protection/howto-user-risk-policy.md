---
title: Så här konfigurerar du riskprincip för användare i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar riskprincip för användare i Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8caf7d0670246d14c5197fda763826b970bd125e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993088"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Instruktioner: Konfigurera riskprincipen för användare

Med användarrisk upptäcker Azure AD att sannolikheten att ett användarkonto har komprometterats. Som administratör kan konfigurera du en villkorlig åtkomst användarprincip, för att automatiskt svarar på en viss användare risknivå.
 
Den här artikeln ger dig den information du behöver att konfigurera en princip för användarrisk.


## <a name="what-is-a-user-risk-policy"></a>Vad är en användarprincip?

Azure AD analyserar varje inloggning för en användare. Målet med analysen är att identifiera misstänkta åtgärder som kommer med inloggningen. Systemet kan identifiera misstänkta åtgärderna är kallas även riskhändelser i Azure AD. När en risk händelser kan identifieras i realtid, det finns även riskhändelser som kräver mer tid. Till exempel kräver systemet för att identifiera en omöjlig resa till ovanliga platser, en inledande inlärningsperiod på 14 dagar för att lära dig om en användares vanligt beteende. Det finns flera alternativ för att lösa identifierade riskhändelserna. Exempelvis kan du kan lösa enskilda riskhändelser manuellt eller du kan hämta dem matchas med en inloggningsrisk eller en villkorlig åtkomstprincip för användarrisk.

Alla riskhändelser som har identifierats för en användare och inte löses kallas active riskhändelser. De aktiva riskhändelser som är kopplade till användaren kallas användarrisk. Azure AD beräknar utifrån användarrisk en sannolikhet (låg, medelhög och hög) att en användare har komprometterats. Sannolikheten kallas risknivån.

![Användaren risker](./media/howto-user-risk-policy/1031.png)

Riskprincip för användare är en automatisk åtgärd som du kan konfigurera för en viss användare risknivå. Du kan använda en riskprincip för användare för att blockera åtkomst till resurser eller kräva ändring av lösenordet för att komma tillbaka ett användarkonto till ett rent tillstånd.


## <a name="how-do-i-access-the-user-risk-policy"></a>Hur kommer jag åt riskprincip för användare?
   
Princip för inloggninsrisk-är i den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för användare](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Principinställningar

När du konfigurerar principen inloggningsrisk, måste du ange:

- Användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-user-risk-policy/11.png)

- Nivå för inloggningsrisk som utlöser principen:

    ![Användarisknivå](./media/howto-user-risk-policy/12.png)

- Vilken typ av åtkomst som du vill tillämpas när din inloggning risknivån är uppfyllt:  

    ![Access](./media/howto-user-risk-policy/13.png)

- Tillståndet för din princip:

    ![Tillämpa princip](./media/howto-user-risk-policy/14.png)

Dialogrutan princip konfiguration får du ett alternativ för att beräkna effekten av din konfiguration.

![Uppskattad påverkan](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan ange en riskprincip för att blockera användare vid inloggning beroende på risknivån.

![Blockerar](./media/howto-user-risk-policy/16.png)


Blockera en inloggning:

* Förhindrar generering av nya riskhändelser för användaren för den berörda användaren
* Gör att administratörer kan manuellt åtgärdar riskhändelser som påverkar användarens identitet och återställer dem till ett säkert tillstånd

## <a name="best-practices"></a>Bästa praxis

Välja en **hög** tröskelvärdet minskar antalet gånger som en princip har utlösts och minimerar påverkan för användare.
Men det omfattar inte **låg** och **medel** användare som har flaggats för risk från principen som inte kanske att skydda identiteter eller enheter som har tidigare eller misstänks äventyras.

När du ställer in principen

* Exkludera användare som sannolikt inte kommer att skapa ett stort antal falskpositiva resultat (utvecklare, säkerhetsanalytiker)
* Exkludera användare i nationella inställningar där aktiverar principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Använd en **hög** tröskelvärde under inledande skala ut, eller om du måste minimera utmaningar som setts av slutanvändare.
* Använd en **låg** tröskelvärdet om din organisation kräver ökad säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att få en balans mellan användbarhet och säkerhet.

En översikt över relaterade användarupplevelsen finns:

* [Komprometterat konto recovery flow](flows.md#compromised-account-recovery).  
* [Komprometterade kontot har spärrats flow](flows.md#compromised-account-blocked).  

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **användarprincip**.

    ![Princip för användarrisk](./media/howto-user-risk-policy/1009.png "riskprincip för användare")




## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
