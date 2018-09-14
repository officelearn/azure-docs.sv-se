---
title: Konfigurera princip för inloggningsrisk i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar Azure AD Identity Protection inloggningsrisk princip.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 7350cbd3e8aed6098f24d0edaa5807d241890287
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581477"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Så här: Konfigurera princip för inloggningsrisk

Azure Active Directory identifierar [riskhändelsetyper](../reports-monitoring/concept-risk-events.md#risk-event-types) i realtid och offline. Varje riskhändelsen har upptäckts för en inloggning för en användare bidrar till ett logiskt koncept som kallas riskfyllda inloggningen. En riskfylld inloggning är du en indikator för en inloggningsförsök som inte kanske har utförts av är tillförlitligt ägare för ett användarkonto.


## <a name="sign-in-risk-level"></a>Risknivå för inloggning

En nivå för inloggningsrisk är en indikation (hög, medel eller låg) på sannolikheten att en inloggningsförsök inte har utförts av är tillförlitligt ägare för ett användarkonto.

## <a name="mitigating-sign-in-risk-events"></a>Minimera riskhändelser som inloggning

En lösning är en åtgärd för att begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet utan att återställa den identitet eller enhet till säkert läge. En lösning kan inte matchas tidigare inloggningsrisk händelser som är associerade med den identitet eller enhet.

Du kan konfigurera säkerhetsprinciper för inloggningsrisk för att minimera riskfyllda inloggningar automatiskt. Med dessa principer kan du överväga att risknivån för användaren eller logga in att blockera riskfyllda inloggningar eller kräva att användaren att utföra multifaktorautentisering. De här åtgärderna kan hindra en angripare utnyttjar en stulen identitet för att skada och kan ge dig tid att skydda identitet.

## <a name="sign-in-risk-security-policy"></a>Säkerhetsprincip för inloggningsrisk
En princip för inloggningsrisk är en villkorlig åtkomstprincip som utvärderar risken för angrepp på en specifik inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1014.png "inloggning riskprincipen")

Azure AD Identity Protection hjälper dig att hantera minskning av riskfyllda inloggningar genom att:

* Ange de användare och grupper som principen gäller för:

    ![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1015.png "inloggning riskprincipen")
* Ange inloggningsrisk nivå tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1016.png "inloggning riskprincipen")
* Ange kontroller som ska tillämpas när principen utlöser:  

    ![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1017.png "inloggning riskprincipen")
* Växla tillståndet för din princip:

    ![MFA-registrering](./media/howto-sign-in-risk-policy/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1018.png "inloggning riskprincipen")

## <a name="what-you-need-to-know"></a>Vad du behöver veta
Du kan konfigurera en säkerhetsprincip för inloggningsrisk för att kräva multifaktorautentisering:

![Riskprincip för inloggning](./media/howto-sign-in-risk-policy/1017.png "inloggning riskprincipen")

Men av säkerhetsskäl fungerar den här inställningen bara för användare som redan har registrerats för multifaktorautentisering. Om villkoret för att kräva multifaktorautentisering uppfylls för en användare som ännu inte har registrerats för multifaktorautentisering, är användaren blockerad.

Som bästa praxis, om du vill kräva multifaktorautentisering för riskfyllda inloggningar, bör du:

1. Aktivera den [registreringsprincip för multi-Factor authentication](#multi-factor-authentication-registration-policy) för de berörda användarna.
2. Kräv att berörda användare att logga in i en icke-riskfyllda session för att utföra en MFA-registrering

Gör så här säkerställer att multifaktorautentisering krävs för en riskfylld inloggning.

## <a name="best-practices"></a>Bästa praxis
Välja en **hög** tröskelvärdet minskar antalet gånger som en princip har utlösts och minimerar påverkan för användare.  

Men det omfattar inte **låg** och **medel** inloggningar som har flaggats för risk från principen som inte blockerar en angripare utnyttjar en komprometterad identitet.

När du ställer in principen

* Undanta användare som inte / kan inte ha multifaktorautentisering
* Exkludera användare i nationella inställningar där aktiverar principen inte är en praktisk (till exempel ingen åtkomst till supportavdelningen)
* Exkludera användare som sannolikt inte kommer att skapa ett stort antal falskpositiva resultat (utvecklare, säkerhetsanalytiker)
* Använd en **hög** tröskelvärde under inledande skala ut, eller om du måste minimera utmaningar som setts av slutanvändare.
* Använd en **låg** tröskelvärdet om din organisation kräver ökad säkerhet. Att välja en **låg** tröskelvärdet introducerar ytterligare användare logga in utmaningar, men ökad säkerhet.

Rekommenderad standard för de flesta organisationer är att konfigurera en regel för en **medel** tröskelvärdet för att få en balans mellan användbarhet och säkerhet.

Princip för inloggningsrisk är:

* Tillämpas på alla webbläsartrafik och inloggningar som använder modern autentisering.
* Inte användas av program med äldre säkerhetsprotokoll genom att inaktivera den WS-Trust-slutpunkten på federerade IDP: N, till exempel AD FS.

Den **riskhändelser** i Identity Protection-konsolen visar en lista över alla händelser:

* Den här principen har tillämpats på
* Du kan granska aktiviteten och fastställa om åtgärden var rätt eller inte

En översikt över relaterade användarupplevelsen finns:

* [Riskfylld inloggning återställning](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Logga in upplevelser med Azure AD Identity Protection](flows.md)  

**Att öppna dialogrutan tillhörande konfigurationer**:

- På den **Azure AD Identity Protection** bladet i den **konfigurera** klickar du på **inloggning riskprincipen**.

    ![Princip för användarrisk](./media/howto-sign-in-risk-policy/1014.png "riskprincip för användare")




## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
