---
title: Så här stänger du active riskhändelser i Azure Active Directory Identity Protection | Microsoft Docs
description: Läs mer om alternativ du har att Stäng active riskhändelser.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e003aec8fa5aeab587fa07acdae3a13b370a535
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521167"
---
# <a name="how-to-close-active-risk-events"></a>Instruktioner: Stäng aktiva riskhändelser

Med [riskhändelser](../reports-monitoring/concept-risk-events.md), Azure Active Directory identifierar indikatorer för potentiellt komprometterade användarkonton. Som administratör, som du vill hämta alla riskhändelser stängda, så att de berörda användarna inte längre är i fara.

Den här artikeln ger en översikt över de ytterligare alternativen som du måste stänga riskhändelser som aktiv.

## <a name="options-to-close-risk-events"></a>Alternativ för att stänga riskhändelser 

Status för en riskhändelse är antingen **active** eller **stängd**. Alla aktiva riskhändelser kan ingå i beräkningen av ett värde med namnet risknivån. Risknivån är en indikator (låg, medelhög och hög) sannolikheten att ett konto har komprometterats. 

Om du vill stänga active riskhändelser har följande alternativ:

- Kräv lösenord för självbetjäning med en princip för användarrisk

- Manuell lösenordsåterställning
 
- Stäng alla riskhändelser 

- Stäng enskilda riskhändelser manuellt



## <a name="require-password-reset-with-a-user-risk-policy"></a>Kräv lösenord för självbetjäning med en princip för användarrisk

Genom att konfigurera [användarriskprincipen för villkorsstyrd åtkomst](howto-user-risk-policy.md) kan du kräva att lösenordet ändras om en viss användarrisknivå har identifierats automatiskt. 

![Återställa lösenord](./media/howto-close-active-risk-events/13.png)

En lösenordsåterställning stängs alla aktiva risker händelser för den relaterade användaren och hämtar tillbaka identiteten till säkert läge. Med hjälp av en princip för användarrisk är det bästa sättet att Stäng active riskhändelser eftersom den här metoden är automatisk. Det finns ingen interaktion krävs mellan den berörda användaren och hjälper dig att supportavdelningen eller en administratör.

Men gäller med hjälp av en princip för användarrisk inte alltid. Detta gäller, till exempel:

- Användare som inte har registrerats för multifaktorautentisering (MFA).
- Användare med aktiva riskhändelser som har tagits bort.
- En undersökning som visar att en rapporterade riskhändelse har utförts av behöriga användare.


## <a name="manual-password-reset"></a>Manuell lösenordsåterställning

Om att kräva ett lösenord återställs via en princip för användarrisk inte är ett alternativ, kan du hämta alla riskhändelser för en användare som avslutas med en manuell lösenordsåterställning.

![Återställa lösenord](./media/howto-close-active-risk-events/04.png)


Relaterade dialogrutan innehåller två olika metoder för att återställa ett lösenord:

![Återställa lösenord](./media/howto-close-active-risk-events/05.png)


**Skapa ett tillfälligt lösenord** -genom att skapa ett tillfälligt lösenord, du kan omedelbart sätta en identitet tillbaka i säkert läge. Den här metoden kräver att interagera med de berörda användarna eftersom de måste veta vad det tillfälliga lösenordet är. Du kan till exempel skicka ett nytt tillfälligt lösenord till en alternativ e-postadressen för användaren eller till användarens chef. Eftersom lösenordet är tillfällig, uppmanas användaren att ändra lösenordet vid nästa inloggningen.


**Användaren måste återställa lösenord** -att kräva att användarna kan återställa lösenord möjliggör self återställning utan att kontakta supportavdelningen eller administratör. T.ex. när det gäller en princip för användarrisk gäller den här metoden endast för användare som har registrerats för MFA. För användare som inte har registrerats för MFA ännu, är det här alternativet inte tillgängligt.


## <a name="dismiss-all-risk-events"></a>Stäng alla riskhändelser

Om ett lösenord återställning är inte ett alternativ för dig, du kan också stänga alla riskhändelser. 

![Återställa lösenord](./media/howto-close-active-risk-events/03.png)

När du klickar på **Ignorera alla händelser**, alla händelser är stängda och den berörda användaren inte längre är i fara. Men eftersom den här metoden inte kan påverka det befintliga lösenordet, se den inte relaterade identiteten till säkert läge. Det prioriterade användningsfallet för den här metoden är en borttagen användare med aktiva riskhändelser. 


## <a name="close-individual-risk-events-manually"></a>Stäng enskilda riskhändelser manuellt

Du kan stänga enskilda riskhändelser manuellt. Genom att stänga riskhändelser manuellt, kan du sänka risknivån. Normalt stängs riskhändelser manuellt som svar på en relaterade undersökning. Till exempel när man talar till så visar en användare att en aktiv riskhändelse inte krävs längre. 
 
När stänga riskhändelser manuellt, kan du vidta någon av följande åtgärder för att ändra status för en riskhändelse:

![Åtgärder](./media/howto-close-active-risk-events/06.png)

- **Lösa** – om när du undersöker en riskhändelse, du har gjort en lämpliga avhjälpande åtgärd utanför Identity Protection och du tror att riskhändelsen bör övervägas stängd, markera händelsen som löst. Matcha händelser som anger den riskhändelsen status till stängd och riskhändelsen kommer inte längre att bidra till användarrisk.

- **Markera som FALSKT positiva** – i vissa fall kan du undersöka en riskhändelse och identifiera att det var felaktigt som flaggats som en riskfylld. Du kan minska antalet sådana händelser genom att markera riskhändelsen som FALSKT positiva. På så sätt maskininlärningsalgoritmer för att förbättra klassificeringen av liknande händelser i framtiden. Status för falsk-positiva händelser är att stängd och kommer inte längre att bidra till användarrisk.

- **Ignorera** – om du inte har vidtagit några Reparationsåtgärd, men vill riskhändelsen som ska tas bort från listan över aktiva, kan du markera en riskhändelse Ignorera och händelsestatus för kommer att stängas. Ignorerade händelser bidrar inte till användarrisk. Det här alternativet bör endast användas under ovanliga omständigheter.

- **Återaktivera** -riskhändelser som stängdes manuellt (genom att välja Lös, falsklarm eller ignorera) kan återaktiveras, anger händelsestatus tillbaka till aktiv. Återaktiverade riskhändelser bidra till den nivå riskberäkning för användaren. Riskhändelser stängda genom reparation (som en säker lösenordsåterställning) kan inte aktiveras.
  

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
