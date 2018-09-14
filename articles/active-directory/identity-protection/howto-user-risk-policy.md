---
title: Så här konfigurerar du riskprincip för användare i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar riskprincip för användare i Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: bcfab9ab95e41b723cb8a8e49d7390a2894d5219
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581372"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Så här: Konfigurera riskprincip för användare

Alla aktiva [riskhändelser](../reports-monitoring/concept-risk-events.md) som identifierades av Azure Active Directory för en användare bidra till ett logiskt koncept som kallas användarrisk. En användare som flaggats för risk är en indikator för ett användarkonto som kan ha komprometterats.

![Användare som har flaggats för risk](./media/howto-user-risk-policy/1200.png)


## <a name="user-risk-level"></a>Användarisknivå

En nivå för användarrisk är en indikation (hög, medel eller låg) på sannolikheten att användarens identitet har komprometterats. Det beräknas baserat på de riskhändelser för användaren som är associerade med en användares identitet.

Status för en riskhändelse är antingen **Active** eller **stängd**. Endast riskhändelser som är **Active** bidra till den nivå riskberäkning för användaren.

Risknivån beräknas med hjälp av följande indata:

* Aktiva riskhändelser som påverkas av användaren
* Risknivån för dessa händelser
* Om eventuella åtgärder har vidtagits

![Användaren risker](./media/howto-user-risk-policy/1031.png "användaren risker")

Du kan använda risknivåer för användare för att skapa principer för villkorlig åtkomst som blockerar riskabla användare från att logga in eller tvinga dem på ett säkert sätt ändra sina lösenord.

## <a name="closing-risk-events-manually"></a>Stänga riskhändelser manuellt

I de flesta fall ska vi ta åtgärder, till exempel ett säkert lösenord för att automatiskt stänga riskhändelser. Men kanske det inte alltid är möjligt.  
Detta gäller, till exempel när:

* En användare med aktiva riskhändelser har tagits bort
* En undersökningen visar att en rapporterade riskhändelse utför av behöriga användare

Eftersom riskhändelser som är **Active** bidra till användaren riskberäkning, du kan behöva manuellt Sänk en risknivå genom att stänga riskhändelser manuellt.  
Under loppet av undersökning, kan du vidta någon av dessa åtgärder för att ändra status för en riskhändelse:

![Åtgärder](./media/howto-user-risk-policy/34.png "åtgärder")

* **Lösa** – om när du undersöker en riskhändelse, du har gjort en lämpliga avhjälpande åtgärd utanför Identity Protection och du tror att riskhändelsen bör övervägas stängd, markera händelsen som löst. Matcha händelser som anger den riskhändelsen status till stängd och riskhändelsen kommer inte längre att bidra till användarrisk.
* **Markera som FALSKT positiva** – i vissa fall kan du undersöka en riskhändelse och identifiera att det var felaktigt som flaggats som en riskfylld. Du kan minska antalet sådana händelser genom att markera riskhändelsen som FALSKT positiva. På så sätt maskininlärningsalgoritmer för att förbättra klassificeringen av liknande händelser i framtiden. Status för falsk-positiva händelser är att **stängd** och kommer inte längre att bidra till användarrisk.
* **Ignorera** – om du inte har vidtagit några Reparationsåtgärd, men vill riskhändelsen som ska tas bort från listan över aktiva, kan du markera en riskhändelse Ignorera och händelsestatus för kommer att stängas. Ignorerade händelser bidrar inte till användarrisk. Det här alternativet bör endast användas under ovanliga omständigheter.
* **Återaktivera** -riskhändelser som stängdes manuellt (genom att välja **lösa**, **falsklarm**, eller **Ignorera**) kan återaktiveras, ställa in händelsen tillbaka till **Active**. Återaktiverade riskhändelser bidra till den nivå riskberäkning för användaren. Riskhändelser stängda genom reparation (som en säker lösenordsåterställning) kan inte aktiveras.

**Att öppna dialogrutan tillhörande konfigurationer**:

1. På den **Azure AD Identity Protection** bladet under **Undersök**, klickar du på **riskhändelser**.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1002.png "manuell för lösenordsåterställning")
2. I den **riskhändelser** klickar du på en risk.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1003.png "manuell för lösenordsåterställning")
3. Högerklicka på en användare på bladet risk.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1004.png "manuell för lösenordsåterställning")

## <a name="closing-all-risk-events-for-a-user-manually"></a>Stänga alla riskhändelser för en användare manuellt
I stället för att manuellt stänga riskhändelser för en användare individuellt, ger Azure Active Directory Identity Protection dig också med en metod för att Stäng alla händelser för en användare med ett enda klick.

![Åtgärder](./media/howto-user-risk-policy/2222.png "åtgärder")

När du klickar på **Ignorera alla händelser**, alla händelser är stängda och den berörda användaren inte längre är i fara.

## <a name="remediating-user-risk-events"></a>Åtgärda användarriskhändelser

En reparation är en åtgärd för att skydda en identitet eller en enhet som har tidigare eller misstänks äventyras. En Reparationsåtgärd återställer identitet eller enhet till säkert läge och löser tidigare riskhändelser som är associerade med den identitet eller enhet.

Om du vill åtgärda riskhändelser för användaren, kan du:

* Utföra ett säkert lösenord för att åtgärda användarriskhändelser manuellt
* Konfigurera en riskprincip för att minimera eller åtgärdar riskhändelser för användaren automatiskt
* Spegla angripen enhet  

### <a name="manual-secure-password-reset"></a>Manuell säker lösenordsåterställning
En säker lösenordsåterställning är en effektiv justering av många riskhändelser och när utförs, automatiskt stängs dessa riskhändelser och beräknar om risknivån. Du kan använda Identity Protection-instrumentpanelen för att initiera en återställning av lösenord för en riskfylld användare.

Relaterade dialogrutan innehåller två olika metoder för att återställa ett lösenord:

**Återställ lösenord** – Välj **kräver att användaren återställer sitt lösenord** så att användaren kan själv återställa om användaren har registrerats för multifaktorautentisering. Vid användarens nästa inloggning, kommer användaren krävs för att lösa en multifaktorautentisering utmaning har och måste sedan ändra lösenordet. Det här alternativet är inte tillgängligt om användarkontot inte är redan registrerad multifaktorautentisering.

**Tillfälligt lösenord** – Välj **skapa ett tillfälligt lösenord** du omedelbart ogiltigförklara det befintliga lösenordet och skapar ett nytt tillfälligt lösenord för användaren. Skicka ett nytt tillfälligt lösenord till en alternativ e-postadressen för användaren eller användarens chef. Eftersom lösenordet är tillfällig, uppmanas användaren att ändra lösenordet vid inloggning.

![Principen](./media/howto-user-risk-policy/1005.png "princip")

**Att öppna dialogrutan tillhörande konfigurationer**:

1. På den **Azure AD Identity Protection** bladet klickar du på **användare som har flaggats för risk**.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1006.png "manuell för lösenordsåterställning")
2. Välj en användare med minst en riskhändelser från listan över användare.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1007.png "manuell för lösenordsåterställning")
3. Klicka på användarbladet **Återställ lösenord**.

    ![Återställning av lösenord för manuell](./media/howto-user-risk-policy/1008.png "manuell för lösenordsåterställning")

## <a name="user-risk-security-policy"></a>Riskprincip
En riskprincip är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare och vidtar åtgärder och minskning åtgärder baserat på fördefinierade villkor och regler.

![Princip för användarrisk](./media/howto-user-risk-policy/1009.png "riskprincip för användare")

Azure AD Identity Protection hjälper dig att hantera minskning och reparation av användare som har flaggats för risk genom att:

* Ange de användare och grupper som principen gäller för:

    ![Princip för användarrisk](./media/howto-user-risk-policy/1010.png "riskprincip för användare")
* Ange användaren risk på tröskelvärdet (låg, medel eller hög) som utlöser principen:

    ![Princip för användarrisk](./media/howto-user-risk-policy/1011.png "riskprincip för användare")
* Ange kontroller som ska tillämpas när principen utlöser:

    ![Princip för användarrisk](./media/howto-user-risk-policy/1012.png "riskprincip för användare")
* Växla tillståndet för din princip:

    ![Princip för användarrisk](./media/howto-user-risk-policy/403.png "MFA-registrering")
* Granska och utvärdera effekten av en ändring innan du aktiverar det:

    ![Princip för användarrisk](./media/howto-user-risk-policy/1013.png "riskprincip för användare")

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

## <a name="mitigating-user-risk-events"></a>Minimera riskhändelser för användaren
Administratörer kan ange en riskprincip för att blockera användare vid inloggning beroende på risknivån.

Blockera en inloggning:

* Förhindrar generering av nya riskhändelser för användaren för den berörda användaren
* Gör att administratörer kan manuellt åtgärdar riskhändelser som påverkar användarens identitet och återställer dem till ett säkert tillstånd


## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
