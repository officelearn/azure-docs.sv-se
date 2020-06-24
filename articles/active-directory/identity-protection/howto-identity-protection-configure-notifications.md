---
title: Azure Active Directory Identity Protection meddelanden
description: Lär dig hur meddelanden stöder dina undersöknings aktiviteter.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9090ca5b8057179b0cbef1d0a87ae563303ed2c1
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130440"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meddelanden

Azure AD Identity Protection skickar två typer av automatiserade e-postmeddelanden som hjälper dig att hantera användar risker och risk identifieringar:

- Användare med skadligt upptäckt e-post
- E-postmeddelande om veckovis sammandrag

Den här artikeln ger en översikt över både e-postaviseringar.

## <a name="users-at-risk-detected-email"></a>Användare med skadligt upptäckt e-post

Som svar på ett identifierat konto vid risk genererar Azure AD Identity Protection en e-postavisering med **användare som riskerar att identifieras** som ämne. E-postmeddelandet innehåller en länk till de användare som har **[flaggats för risk](../reports-monitoring/concept-user-at-risk.md)** rapporten. Som bästa praxis bör du omedelbart undersöka de användare som är utsatta för risk.

Med konfigurationen för den här aviseringen kan du ange på vilken nivå för användar risk du vill att aviseringen ska genereras. E-postmeddelandet skapas när användarens risk nivå når det som du har angett. Om du till exempel ställer in principen för att avisera om medelhög användar risk och användarens användar risk Poäng flyttar till medelhög risk på grund av en inloggnings risk i real tid, får du användarna ett hot som upptäckts via e-post. Om användaren har efterföljande risk identifieringar som gör att beräkning av användar risk nivån är den angivna risk nivån (eller högre) får du ytterligare användare vid risk identifiering av e-postmeddelanden när risken för användar risker beräknas om. Om en användare till exempel flyttar till medelhög risk den 1 januari får du ett e-postmeddelande om dina inställningar är inställda på varning för medelhög risk. Om samma användare sedan har en annan risk identifiering den 5 januari som också är medels Tor risk, och användar risk poängen beräknas om och är i hög grad, kommer du att få ett annat e-postmeddelande. 

En ytterligare e-postavisering skickas dock endast om den tidpunkt då risk identifieringen inträffade (som orsakade ändringen av användar risk nivån) är senare än när den senaste e-postmeddelandet skickades. Till exempel är ett användar inloggnings program den 1 januari kl. 5 och det inte finns någon real tids risk (vilket innebär att ingen e-post genereras på grund av inloggningen). Tio minuter senare, kl. 5:10 är samma användare som loggar in igen och har hög risk i real tid, vilket gör att användar risk nivån går över till hög och ett e-postmeddelande som ska skickas. Vid 5:15 AM ändras offline-risk poängen för den ursprungliga inloggningen med 5 till hög risk på grund av bearbetning av offline-risker. Det gick inte att skicka en ytterligare användare som har flaggats för risk-e-post, eftersom tiden för den första inloggningen var före den andra inloggningen som redan utlöste ett e-postmeddelande.

För att förhindra en överlagring av e-postmeddelanden får du bara en användare vid en risk för att identifiera ett e-postmeddelande inom en tids period på 5 sekunder. Det innebär att om flera användare flyttar till den angivna risk nivån under samma 5 andra tids period, kommer vi att aggregera och skicka ett e-postmeddelande för att representera ändringen av risk nivån för alla.

![Användare med skadligt upptäckt e-post](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurera användare med risk identifierade aviseringar

Som administratör kan du ange:

- **Den användar risk nivå som utlöser genereringen av det här e-postmeddelandet** som standard är risk nivån inställd på "hög" risk.
- Mottagare **av det här e-postmeddelandet** är som standard alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhets administratörer och säkerhets läsare som mottagare.
   - Du kan också **lägga till ytterligare e-postmeddelanden för att få aviseringar om** den här funktionen är en förhands granskning och användare som har definierats måste ha rätt behörighet för att visa de länkade rapporterna i Azure Portal.

Konfigurera användarna i ett riskfylldt e-postmeddelande i **Azure Portal** under **Azure Active Directory**  >  **säkerhets**  >  **identitets skydd**  >  **användare vid risk identifierings aviseringar**.

## <a name="weekly-digest-email"></a>E-postmeddelande om veckovis sammandrag

E-postmeddelandet om veckovis sammandrag innehåller en sammanfattning av nya risk identifieringar.  
Den innehåller:

- Nya riskfyllda användare har identifierats
- Nya riskfyllda inloggningar upptäcktes (i real tid)
- Länkar till relaterade rapporter i Identity Protection

![E-postmeddelande om veckovis sammandrag](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Som standard inkluderar mottagarna alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhets administratörer och säkerhets läsare som mottagare.

### <a name="configure-weekly-digest-email"></a>Konfigurera e-post för veckovis sammandrag

Som administratör kan du växla att skicka ett e-postmeddelande med veckovis sammanfattning på eller av och välja de användare som ska tilldelas e-postmeddelandet.

Konfigurera e-postmeddelandet om veckovis sammandrag i **Azure Portal** under **Azure Active Directory**  >  **säkerhet**för  >  **Identity Protection**  >  **veckovis sammandrag**av säkerhets identitet.

## <a name="see-also"></a>Se även

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
