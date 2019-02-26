---
title: Vanliga frågor och kända problem med identity protection (uppdateras) i Azure Active Directory | Microsoft Docs
description: Vanliga frågor och kända problem med identity protection (uppdateras) i Azure Active Directory.
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
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1cc8a9305103a30fc0ea0fc40c23a522e136d80
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807192"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Vanliga frågor och kända problem med identity protection (uppdateras) i Azure Active Directory


## <a name="dismiss-user-risk-known-issues"></a>Stäng användarrisk kända problem

**Stäng användarrisk** i klassisk Identity Protection anger aktören i användarens Riskhistorik i Identity Protection (uppdateras) till **Azure AD**.


**Stäng användarrisk** i Identity Protection (uppdateras) anger aktören i användarens Riskhistorik i Identity Protection (uppdateras) till **\<administratörens namn med en hyperlänk som pekar på användarens blad\>**.

Det finns en aktuell känt problem som orsakar fördröjningar i användarflödet risk avsked. Om du har en ”användarprincip” sluta den här principen att använda avvisade användare inom minuter efter att klicka på ”Stäng användarrisk”. Men finns det kända förseningar med UX uppdaterar ”Risk statusen” Avvisade användare. Uppdatera sidan på nivå webbläsare om du vill se senaste användare ”risktillstånd” som en lösning.


## <a name="risky-users-report-known-issues"></a>Riskfylld användare rapporterar kända problem

Frågar på den **användarnamn** fält är skiftlägeskänsliga, vid frågor om den **namn** fält är fallet-oberoende.

Växla **visa datum som** döljer den **risken SENAST UPPDATERADES** kolumn. Att readd kolumnen Klicka **kolumner** överst på bladet riskfylld användare.

**Stäng alla händelser** i klassisk Identity Protection anger statusen för riskhändelser till **stängd (löst)**.


## <a name="risky-sign-ins-report-known-issues"></a>Rapporten om riskfyllda inloggningar kända problem

**Lösa** på en risk händelsen anger statusen till **användare skickas MFA som styrs av en princip för riskbaserad**.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Varför kan jag in min egen risknivåer för varje riskhändelsen?

Risknivåer i Identity Protection baseras på precisionen för identifieringen och drivs av våra övervakade machine learning. Om du vill anpassa användarnas upplevelse presenteras administratör kan inkludera/exkludera vissa användare/grupper från Användarrisk och logga In principer för Åtkomstrisk.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Varför platsen för en inloggning inte matchar där användaren verkligen loggat in från?

IP-geoplats mappningen är en branschomfattande utmaning. Om du anser att den plats som anges i rapporten inloggningar inte matchar den faktiska platsen, kontaktar du support. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Hur fungerar återkopplingsmekanismerna i Identity Protection?

**Bekräfta komprometteras** (på en inloggning) – informerar Azure AD Identity Protection som inloggningen inte utförs av identitet ägare och anger en kompromiss.

- Vid mottagning av denna feedback kan vi flytta risktillstånd inloggning och användare till **bekräftat komprometteras** och risknivå till **hög**.

- Dessutom ger vi informationen till vår machine learning-system för framtida förbättringar i riskbedömning.

    > [!NOTE]
    > Om användaren redan har åtgärdats inte klickar du på **bekräfta komprometteras** eftersom flyttas risktillstånd inloggning och användare till **bekräftat komprometteras** och risknivå till **hög**.

**Bekräfta säker** (på en inloggning) – informerar du Azure AD Identity Protection att inloggningen har utförts av ägaren identitet och anger inte en kompromettering.

- Vid mottagning av denna feedback kan vi flytta inloggning (inte användare) riskerar tillstånd att **bekräftat safe** och risknivå till **-**.

- Dessutom ger vi informationen till vår machine learning-system för framtida förbättringar i riskbedömning.

    > [!NOTE]
    > Om du tror att användaren inte komprometteras, kan du använda **stänga användarrisk** på användarnivå istället för att använda **bekräftat safe** på nivån för inloggning. En **stänga användarrisk** nivå på användaren stänger risken för användaren och alla föregående riskfyllda inloggningar och riskhändelser.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Varför ser jag en användare med låg (eller senare) riskpoäng, även om inga riskfyllda inloggningar eller riskhändelser visas i Identity Protection?

Får användaren risk är kumulativ sin natur och inte upphör att gälla, en användare kan ha en användarrisk för låg eller ovan även om det finns inga senaste riskfyllda inloggningar eller riskhändelser som visas i Identity Protection. Detta kan inträffa om endast skadlig aktivitet för en användare som är drabbad, utöver en mer specifik tidsram som vi lagrar information om riskfyllda inloggningar och riskhändelser. Vi inte upphör att gälla användarrisk eftersom illvilliga aktörer har rapporterats vara i kundens miljö över 140 dagar bakom en komprometterad identitet innan av dig attacken. Kunder kan granska användarens risk tidslinje för att förstå varför en användare är i fara genom att gå till: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Varför en inloggning har en ”logga in (aggregering)” riskpoäng på hög när identifieringar som är associerade med det är för låg eller medelhög risk?

Hög sammanställd riskpoäng kan baseras på andra funktioner i inloggningen eller det faktum att mer än en identifiering utlöses för att logga in. Och omvänt, loggar in kan ha en inloggningsrisk (mängdfunktioner) medium även om identifieringar som är associerade med inloggningen är av hög risk. 
