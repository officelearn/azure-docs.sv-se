---
title: Så här att förbättra identifieringen i Azure Active Directory Identity Protection (uppdateras) | Microsoft Docs
description: Så här att förbättra identifieringen i Azure Active Directory Identity Protection (uppdateras).
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
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 2ba6e3b8b844d72b762e9a66629f16b36193a69f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478397"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Instruktioner: Förbättra identifieringen 

Identity Protection tillhandahåller mekanismer för att ge feedback till Azure AD på risk identifieringar i miljön. Du kan bekräfta status för identifierade riskfylld användare eller logga in händelsen du vill ge feedback. Användare med Microsoft denna feedback att vidta åtgärder för den aktuella risk identifieringar och förbättra framtida identifieringar. 


## <a name="what-is-detection"></a>Vad är identifiering?

Identifiering är en process för att identifiera misstänkta aktiviteter tillsammans med dina användarkonton. Azure AD kan identifiera misstänkta aktiviteter kallas [riskhändelsen](../reports-monitoring/concept-risk-events.md). Identifieringsprocessen baseras på anpassningsbar machine learning-algoritmer och heuristik för att identifiera riskhändelser för användare.

Resultaten av programuppdateringsidentifieringen används för att avgöra om användare och inloggningar är i fara. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Hur kan jag för att förbättra identifieringen?

Eftersom identifiering är en automatiserad process är det möjligt att Azure AD rapporterar falska positiva identifieringar. Du kan förbättra identifieringen genom att tillhandahålla feedback till Azure AD om resultaten av programuppdateringsidentifieringen.

Det finns tre sätt att förbättra identifieringen: Bekräfta komprometterade inloggning, comfirm säker inloggning och Stäng användarrisk. Du kan göra det från följande rapporter:

- **Rapporten om riskfyllda inloggningar -** i rapporten över riskfyllda inloggningar kan du bekräfta att inloggningar är säker eller skadade

- **Riskfylld användare rapport -** i rapporten riskabla användare kan du stänga användarrisk 

Din feedback bearbetas av Azure AD för att förbättra resultaten av programuppdateringsidentifieringen. Normalt kan ge du feedback som en del av en användarrisk eller inloggningsrisk undersökning. Mer information finns i [hur du undersöker riskabla användare och inloggningar](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Bekräfta komprometterade

Bekräfta en händelse som inloggning som komprometteras signalerar till Azure AD att inloggningen inte har behörighet av ägaren identitet. När du väljer ”bekräfta komprometteras”, kommer Azure AD

- Öka risken för användare av den berörda användaren till hög.

- Optimera maskininlärning som identifierar riskhändelser
 
- Utföra ytterligare åtgärder för att ytterligare skydda din organisation



Kontrollera en komprometterad inloggning:

- **Rapporten över riskfyllda inloggningar** – det här alternativet kan du bekräfta en komprometterad inloggning för en eller flera inloggningshändelser.

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/07.png)

- **Detaljer om rapporten över riskfyllda inloggningar** – det här alternativet kan du bekräfta ett komprometterat konto för den valda inloggning händelsen i rapporten över riskfyllda inloggningar. 

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Bekräfta säker


Bekräfta en händelse som inloggning som säker signaler till Azure AD som inloggningen **har** behörighet av ägaren respektive identitet. När du väljer ”bekräfta säker”, kommer Azure AD:

- Återställ användaren risk bidraget för de valda inloggningarna

- Stäng de underliggande riskhändelser

- Optimera maskininlärning som identifierar riskhändelser

- Utföra ytterligare åtgärder för att ytterligare skydda din organisation
 

Kontrollera en säker inloggning i:

- **Rapporten över riskfyllda inloggningar** – det här alternativet kan du bekräfta en säker inloggning för en eller flera inloggningshändelser.

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/08.png)

- **Detaljer om rapporten över riskfyllda inloggningar** – det här alternativet kan du bekräfta en säker inloggning för den valda inloggning händelsen i rapporten över riskfyllda inloggningar. 

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Ignorera användarrisken

Om du har redan vidtagit åtgärder för en användare för risker eller tror att de har felaktigt markerats som riskfylld, kan du stänga risken för en användare. Stänger en användarrisk återställer du till ett icke-riskfyllda tillstånd. Alla tidigare riskfyllda inloggningar och risk kommer händelser för den valda användaren att avvisas.


Du kan ignorera det rapporterade användarrisk i:

- **Riskfylld användare rapporten** – det här alternativet kan du stänga användaren risken för en eller flera markerade användare.

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/02.png)

- **Vyn Detaljer** – det här alternativet kan du ignorera användarrisk för den valda användaren i risk över användare. 

    ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/01.png)


**Vad du bör känna till:**

- Du kan inte ångra den här åtgärden.

- Det kan ta några minuter för att åtgärden ska slutföras, vilket är anledningen till att du inte ska skicka din begäran igen.

- Du kan bara utföra den här åtgärden om AD hanterar autentiseringsuppgifterna för användaren. (Vad gör detta medelvärdet?)



## <a name="best-practices"></a>Bästa praxis

Stänger en användarrisk är ett sätt att avblockera dem om de har blockerats av riskprincip för användare och kan inte åtgärda self på grund av att man inte behöver återställning av lösenord och/eller MFA är aktiverat. I det här fallet är det bäst att se till att användare sedan registrerar för återställning av lösenord och MFA så att de kan reparera eventuella framtida riskhändelser.


## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview-v2.md).


