---
title: Konfigurera princip för inloggningsrisk i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar Azure AD Identity Protection inloggningsrisk princip.
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
ms.openlocfilehash: da3f03cfcf821fa093e9f21cf9a11a2d99d67683
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999036"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Instruktioner: Konfigurera riskprincipen för inloggning

Azure Active Directory identifierar [riskhändelsetyper](../reports-monitoring/concept-risk-events.md#risk-event-types) i realtid och offline. Varje riskhändelsen har upptäckts för en inloggning för en användare bidrar till ett logiskt koncept som kallas riskfyllda inloggningen. En riskfylld inloggning är du en indikator för en inloggningsförsök som inte kanske har utförts av är tillförlitligt ägare för ett användarkonto.


## <a name="what-is-the-sign-in-risk-policy"></a>Vad är inloggningsrisk principen?

Azure AD analyserar varje inloggning för en användare. Målet med analysen är att identifiera misstänkta åtgärder som kommer med inloggningen. Till exempel är inloggningen klar med hjälp av en anonym IP-adress eller är inloggning som initieras från en okänd plats? Systemet kan identifiera misstänkta åtgärderna är kallas även riskhändelser i Azure AD. Baserat på de riskhändelser som har identifierats under inloggning, Azure AD beräknar ett värde. Värdet som representerar sannolikheten (låg, medelhög och hög) att inloggningen inte utförs av behöriga användare. Sannolikheten kallas **risknivå för inloggning**.

Princip för inloggningsrisk är en automatisk åtgärd som du kan konfigurera för en specifik inloggning risknivå. Du kan blockera åtkomst till resurser eller kräver skicka multifaktorautentisering (MFA) svårt att få åtkomst i ditt svar.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hur kommer jag åt inloggningsrisk principen?
   
Princip för inloggninsrisk-är i den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1014.png "inloggning riskprincipen")


## <a name="policy-settings"></a>Principinställningar

När du konfigurerar principen inloggningsrisk, måste du ange:

- Användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-sign-in-risk-policy/11.png)

- Nivå för inloggningsrisk som utlöser principen:

    ![Risknivå för inloggning](./media/howto-sign-in-risk-policy/12.png)

- Vilken typ av åtkomst som du vill tillämpas när din inloggning risknivån är uppfyllt:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- Tillståndet för din princip:

    ![Tillämpa princip](./media/howto-sign-in-risk-policy/14.png)


Dialogrutan princip konfiguration får du ett alternativ för att beräkna effekten av omkonfiguration.

![Uppskattad påverkan](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan konfigurera en säkerhetsprincip för inloggningsrisk för att kräva MFA:

![Kräv MFA](./media/howto-sign-in-risk-policy/16.png)

Men av säkerhetsskäl fungerar den här inställningen bara för användare som redan har registrerats för MFA. Identitetsskydd blockerar användare med en MFA-kravet om de inte är ännu registrerats för MFA.

Om du vill kräva MFA för riskfyllda inloggningar, bör du:

1. Aktivera den [registreringsprincip för multi-Factor authentication](howto-mfa-policy.md) för de berörda användarna.

2. Kräv att berörda användare att logga in på en icke-riskfyllda session att utföra en MFA-registrering.

Gör så här säkerställer att multifaktorautentisering krävs för en riskfylld inloggning.

Princip för inloggningsrisk är:

- Tillämpas på alla webbläsartrafik och inloggningar som använder modern autentisering.

- Inte användas av program med äldre säkerhetsprotokoll genom att inaktivera den WS-Trust-slutpunkten på federerade IDP: N, till exempel AD FS.


En översikt över relaterade användarupplevelsen finns:

* [Riskfylld inloggning återställning](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Logga in upplevelser med Azure AD Identity Protection](flows.md)  

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

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
