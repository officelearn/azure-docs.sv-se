---
title: Så här konfigurerar du principer för åtkomstrisk i Azure Active Directory identity protection (uppdateras) | Microsoft Docs
description: Så här konfigurerar du principer för åtkomstrisk i Azure Active Directory identity protection (uppdateras).
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dd988004112fa4f420af7d0149bde19fdc06bfc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958360"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Instruktioner: Konfigurera principer för risk i Azure Active Directory identity protection (uppdateras)


Azure AD upptäcker riskhändelser som indikatorer för potentiellt komprometterade identiteter. Du kan definiera automatiska svar på resultaten av programuppdateringsidentifieringen genom att konfigurera principer för åtkomstrisk:

- Du kan konfigurera ett svar till i realtid riskhändelser som har identifierats under en användares inloggning med inloggningsrisk-principen. 
- Du kan konfigurera ett svar till alla aktiva risker som har identifierats för en användare över tid med riskprincip för användare.  

> [!VIDEO https://youtu.be/zEsbbik-BTE]
## <a name="what-is-the-sign-in-risk-policy"></a>Vad är inloggningsrisk principen?

Azure AD analyserar varje inloggning för en användare. Målet med analysen är att identifiera misstänkta åtgärder som kommer med inloggningen. Till exempel är inloggningen klar med hjälp av en anonym IP-adress eller är inloggning som initieras från en okänd plats? Systemet kan identifiera misstänkta åtgärderna är kallas även riskhändelser i Azure AD. Baserat på de riskhändelser som har identifierats under inloggning, Azure AD beräknar ett värde. Värdet som representerar sannolikheten (låg, medelhög och hög) att inloggningen inte utförs av behöriga användare. Sannolikheten kallas **risknivå för inloggning**.

Princip för inloggningsrisk är en automatisk åtgärd som du kan konfigurera för en specifik inloggning risknivå. Du kan blockera åtkomst till resurser eller kräver skicka multifaktorautentisering (MFA) svårt att få åtkomst i ditt svar.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hur kommer jag åt inloggningsrisk principen?
   
Princip för inloggninsrisk-är i den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för inloggning](./media/howto-configure-risk-policies/1014.png "inloggning riskprincipen")


## <a name="sign-in-risk-policy-settings"></a>Inställningar för inloggningsrisk

När du konfigurerar principen inloggningsrisk, måste du ange:

- Användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-configure-risk-policies/11.png)

- Nivå för inloggningsrisk som utlöser principen:

    ![Risknivå för inloggning](./media/howto-configure-risk-policies/12.png)

- Vilken typ av åtkomst som du vill tillämpas när din inloggning risknivån är uppfyllt:  

    ![Access](./media/howto-configure-risk-policies/13.png)

- Tillståndet för din princip:

    ![Tillämpa princip](./media/howto-configure-risk-policies/14.png)


Dialogrutan princip konfiguration får du ett alternativ för att beräkna effekten av omkonfiguration.

![Uppskattad påverkan](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Vad du bör veta om principer för inloggningsrisk

Du kan konfigurera en säkerhetsprincip för inloggningsrisk för att kräva MFA:

![Kräv MFA](./media/howto-configure-risk-policies/16.png)

Men av säkerhetsskäl fungerar den här inställningen bara för användare som redan har registrerats för MFA. Identitetsskydd blockerar användare med en MFA-kravet om de inte är ännu registrerats för MFA.

Om du vill kräva MFA för riskfyllda inloggningar, bör du:

1. Aktivera registreringsprincip för multi-Factor authentication för användare som påverkas.

2. Kräv att berörda användare att logga in i en icke-riskfyllda session för att utföra en MFA-registrering.

Gör så här säkerställer att multifaktorautentisering krävs för en riskfylld inloggning.

Princip för inloggningsrisk är:

- Tillämpas på alla webbläsartrafik och inloggningar som använder modern autentisering.

- Inte användas av program med äldre säkerhetsprotokoll genom att inaktivera den WS-Trust-slutpunkten på federerade IDP: N, till exempel AD FS.


En översikt över relaterade användarupplevelsen finns:

* [Riskfylld inloggning återställning](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Logga in upplevelser med Azure AD Identity Protection](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>Vad är en användarprincip?

Azure AD analyserar varje inloggning för en användare. Målet med analysen är att identifiera misstänkta åtgärder som kommer med inloggningen. Systemet kan identifiera misstänkta åtgärderna är kallas även riskhändelser i Azure AD. När en risk händelser kan identifieras i realtid, det finns även riskhändelser som kräver mer tid. Till exempel kräver systemet för att identifiera en omöjlig resa till ovanliga platser, en inledande inlärningsperiod på 14 dagar för att lära dig om en användares vanligt beteende. Det finns flera alternativ för att lösa identifierade riskhändelserna. Exempelvis kan du kan lösa enskilda riskhändelser manuellt eller du kan hämta dem matchas med en inloggningsrisk eller en villkorlig åtkomstprincip för användarrisk.

Alla riskhändelser som har identifierats för en användare och inte löses kallas active riskhändelser. De aktiva riskhändelser som är kopplade till användaren kallas användarrisk. Azure AD beräknar utifrån användarrisk en sannolikhet (låg, medelhög och hög) att en användare har komprometterats. Sannolikheten kallas risknivån.

![Användaren risker](./media/howto-configure-risk-policies/11031.png)

Riskprincip för användare är en automatisk åtgärd som du kan konfigurera för en viss användare risknivå. Du kan använda en riskprincip för användare för att blockera åtkomst till resurser eller kräva ändring av lösenordet för att komma tillbaka ett användarkonto till ett rent tillstånd.


## <a name="how-do-i-access-the-user-risk-policy"></a>Hur kommer jag åt riskprincip för användare?
   
Riskprincip för användare som tillhör den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för användare](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Användarprincipinställningar för risk

När du konfigurerar riskprincip för användare, måste du ange:

- Användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-configure-risk-policies/111.png)

- Nivå för inloggningsrisk som utlöser principen:

    ![Användarisknivå](./media/howto-configure-risk-policies/112.png)

- Vilken typ av åtkomst som du vill tillämpas när din inloggning risknivån är uppfyllt:  

    ![Access](./media/howto-configure-risk-policies/113.png)

- Tillståndet för din princip:

    ![Tillämpa princip](./media/howto-configure-risk-policies/114.png)

Dialogrutan princip konfiguration får du ett alternativ för att beräkna effekten av din konfiguration.

![Uppskattad påverkan](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Vad du bör veta om användarrisk principer

Du kan ange en riskprincip för att blockera användare vid inloggning beroende på risknivån.

![Blockerar](./media/howto-configure-risk-policies/116.png)


Blockera en inloggning:

* Förhindrar generering av nya riskhändelser för användaren för den berörda användaren
* Gör att administratörer kan manuellt åtgärdar riskhändelser som påverkar användarens identitet och återställer dem till ett säkert tillstånd


























## <a name="best-practices"></a>Bästa praxis

Välja en **hög** tröskelvärdet minskar antalet gånger som en princip har utlösts och minimerar påverkan för användare.  

Men det omfattar inte **låg** och **medel** inloggningar som har flaggats för risk från principen som inte blockerar en angripare utnyttjar en komprometterad identitet.

När du ställer in principen

- Undanta användare som inte / kan inte ha multifaktorautentisering

- Exkludera användare i nationella inställningar där aktiverar principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)

- Exkludera användare som sannolikt inte kommer att generera många falskpositiva resultat (utvecklare, säkerhetsanalytiker)

- Använd en **hög** tröskelvärde under inledande princip lansering, eller om du måste minimera utmaningar som setts av slutanvändare.

- Använd en **låg** tröskelvärdet om din organisation kräver ökad säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att få en balans mellan användbarhet och säkerhet.





## <a name="next-steps"></a>Nästa steg

 [Channel 9: Azure AD och Identity visa: Förhandsversionen av Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

