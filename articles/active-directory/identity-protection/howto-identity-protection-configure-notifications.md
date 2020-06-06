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
ms.openlocfilehash: c7b5208d2da3635e822049859cae9c8f17b6105a
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464257"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meddelanden

Azure AD Identity Protection skickar två typer av automatiserade e-postmeddelanden som hjälper dig att hantera användar risker och risk identifieringar:

- Användare med skadligt upptäckt e-post
- E-postmeddelande om veckovis sammandrag

Den här artikeln ger en översikt över både e-postaviseringar.

## <a name="users-at-risk-detected-email"></a>Användare med skadligt upptäckt e-post

Som svar på ett identifierat konto vid risk genererar Azure AD Identity Protection en e-postavisering med **användare som riskerar att identifieras** som ämne. E-postmeddelandet innehåller en länk till de användare som har **[flaggats för risk](../reports-monitoring/concept-user-at-risk.md)** rapporten. Som bästa praxis bör du omedelbart undersöka de användare som är utsatta för risk.

Med konfigurationen för den här aviseringen kan du ange på vilken nivå för användar risk du vill att aviseringen ska genereras. E-postmeddelandet skapas när användarens risk nivå når det du har angett. du får dock inga nya användare när de riskerar att identifiera e-postaviseringar för den här användaren när de har flyttat till den här användar risk nivån. Om du t. ex. ställer in principen för att varna om medelhög användar risk och din användare John flyttar till medelhög risk, får användarna ett hot som upptäckts via e-post för John. Du får dock ingen andra användare vid risk identifiering om John sedan flyttar till hög risk eller har ytterligare risk identifieringar.

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
