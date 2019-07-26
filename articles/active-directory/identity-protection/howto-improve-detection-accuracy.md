---
title: Förbättra identifierings precisionen i Azure Active Directory Identity Protection (uppdateras) | Microsoft Docs
description: Förbättra identifierings precisionen i Azure Active Directory Identity Protection (uppdateras).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333952"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Instruktioner: Förbättra identifierings precisionen 

Identity Protection tillhandahåller mekanismer för att ge feedback till Azure AD på risk identifieringar i din miljö. Om du vill ge feedback kan du bekräfta statusen identifierad riskfylld användare eller inloggnings händelse. Microsoft-användare den här feedbacken för att vidta åtgärder gällande risk identifiering och förbättra noggrannheten i framtida identifieringar. 

## <a name="what-is-detection"></a>Vad är identifiering?

Identifiering är en process för att identifiera misstänkta aktiviteter tillsammans med dina användar konton. De misstänkta aktiviteter som Azure AD kan identifiera kallas för [risk händelser](../reports-monitoring/concept-risk-events.md). Identifierings processen baseras på anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera risk händelser för användare.

Identifierings resultaten används för att avgöra om användare och inloggningar är i fara. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Hur kan jag förbättra identifierings precisionen?

Eftersom identifiering är en automatiserad process är det möjligt att Azure AD rapporterar falska positiva identifieringar. Du kan förbättra identifierings precisionen genom att ge feedback till Azure AD om identifierings resultaten.

Det finns tre sätt att förbättra identifierings precisionen: bekräfta komprometterad inloggning, bekräfta säker inloggning och Stäng av användar risk. Du kan göra det från följande rapporter:

- **Rapport över riskfyllda inloggningar –** I rapporten riskfyllda inloggningar kan du bekräfta att inloggningar är säkra eller komprometterade
- **Rapport över riskfyllda användare –** I rapporten riskfyllda användare kan du ignorera användar risk 

Din feedback bearbetas av Azure AD för att förbättra noggrannheten i identifierings resultaten. Normalt ger du feedback som en del av en användar risk eller inloggnings risk undersökning. Mer information finns i [så här undersöker du riskfyllda användare och inloggningar](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Bekräfta kompromettering

Bekräfta en inloggnings händelse som komprometterade signaler till Azure AD att inloggningen inte auktoriserats av identitets ägaren. När du väljer "bekräfta komprometterad" kommer Azure AD att

- Öka användar risken för den berörda användaren till hög.
- Hjälp för att optimera Machine Learning som identifierar risk händelser
- Vidta ytterligare åtgärder för att skydda din organisation ytterligare

Bekräfta en komprometterad inloggning:

- **Rapporten riskfyllda inloggningar** – med det här alternativet kan du bekräfta en komprometterad inloggning för en eller flera inloggnings händelser.

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/07.png)

- **Vyn detaljer i rapporten över riskfyllda inloggningar** – det här alternativet gör att du kan bekräfta ett komprometterat konto för den valda inloggnings händelsen i rapporten riskfyllda inloggningar. 

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Bekräfta säker

Bekräfta en inloggnings händelse som säkra signaler till Azure AD att inloggningen **auktoriserats** av respektive identitets ägare. När du väljer "bekräfta säker" kommer Azure AD att:

- Återställ användar risk bidraget för de valda inloggningarna
- Stäng de underliggande risk händelserna
- Hjälp för att optimera Machine Learning som identifierar risk händelser
- Vidta ytterligare åtgärder för att skydda din organisation ytterligare
 
Bekräfta en säker inloggning i:

- **Rapporten riskfyllda inloggningar** – med det här alternativet kan du bekräfta en säker inloggning för en eller flera inloggnings händelser.

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/08.png)

- **Vyn detaljer i rapporten över riskfyllda inloggningar** – det här alternativet gör att du kan bekräfta en säker inloggning för den valda inloggnings händelsen i rapporten riskfyllda inloggningar. 

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Ignorera användarrisken

Om du redan har vidtagit åtgärder för en risk användare eller om du tror att de har marker ATS som riskfyllda, kan du stänga av en användares risk. Om du stänger av en användares risk återställs användaren till ett icke-riskfylldt tillstånd. Alla tidigare riskfyllda inloggningar och risk händelser för den valda användaren stängs.

Du kan stänga av den rapporterade användar risken i:

- **Rapporten riskfyllda användare** – det här alternativet gör att du kan stänga av användar risken för en eller flera valda användare.

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/02.png)

- **Vyn detaljer** – med det här alternativet kan du stänga användar risken för den valda användaren i användar risk rapporten. 

   ![Ignorera användarrisken](./media/howto-improve-detection-accuracy/01.png)

**Vad du bör känna till:**

- Du kan inte återställa den här åtgärden.
- Det kan ta några minuter att slutföra åtgärden, vilket är anledningen till att du inte ska skicka in din begäran igen.
- Du kan bara utföra den här åtgärden om AD hanterar användarens autentiseringsuppgifter. 

## <a name="best-practices"></a>Bästa praxis

Att stänga av en användares risk är ett sätt att avblockera dem om de har blockerats av principen för användar risk och inte kan åtgärdas på grund av att lösen ords återställning och/eller MFA aktive ras. I den här situationen är det bäst att se till att användaren registrerar sig för återställning av lösen ord och MFA så att de kan åtgärda eventuella framtida risk händelser själv.

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview-v2.md).
