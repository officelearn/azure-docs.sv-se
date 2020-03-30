---
title: Azure Active Directory-identitetsskyddsmeddelanden
description: Läs om hur aviseringar stöder dina undersökningsaktiviteter.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120125"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory-identitetsskyddsmeddelanden

Azure AD Identity Protection skickar två typer av automatiska e-postmeddelanden för att hjälpa dig att hantera användarrisk- och riskidentifieringar:

- Användare i riskzonen upptäckte e-post
- Veckovis sammandragsmeddelande

Den här artikeln ger dig en översikt över båda e-postmeddelanden.

## <a name="users-at-risk-detected-email"></a>Användare i riskzonen upptäckte e-post

Som svar på ett upptäckt konto i riskzonen genererar Azure AD Identity Protection en e-postavisering med **användare i riskzonen som identifieras** som ämne. E-postmeddelandet innehåller en länk till **[den användare som flaggats för riskrapport.](../reports-monitoring/concept-user-at-risk.md)** Bästa praxis bör du omedelbart undersöka användarna i riskzonen.

Konfigurationen för den här aviseringen gör att du kan ange på vilken användarrisknivå du vill att aviseringen ska genereras. E-postmeddelandet genereras när användarens risknivå når det du har angett. Du får dock inte nya användare i riskzonen upptäckta e-postmeddelanden för den här användaren när de flyttar till den här användarrisknivån. Om du till exempel anger att principen ska aviseras på medelanvändarrisk och användaren John flyttar till medelhög risk får du de användare som har identifierats via risk för e-post för John. Du får dock inte en andra användare i riskzonen upptäckt varning om John sedan flyttar till hög risk eller har ytterligare riskidentifiering.

![Användare i riskzonen upptäckte e-post](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurera användare med riskde upptäckta aviseringar

Som administratör kan du ställa in:

- **Användarrisknivån som utlöser genereringen av det här e-postmeddelandet** - Som standard är risknivån inställd på "Hög" risk.
- **Mottagarna av det här e-postmeddelandet** - Som standard inkluderar mottagarna alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhetsadministratörer, säkerhetsläsare som mottagare.
   - Du kan också **lägga till ytterligare e-postmeddelanden för att få aviseringar** om den här funktionen är en förhandsgranskning och användare som definieras måste ha rätt behörighet för att kunna visa länkade rapporter i Azure-portalen.

Konfigurera användare med risk e-post i **Azure-portalen** under **Azure Active Directory** > **Security** > Identity**Protection** > Användare i**riskzonen upptäckta aviseringar**.

## <a name="weekly-digest-email"></a>Veckovis sammandragsmeddelande

Den veckovisa sammanfattningen e-post innehåller en sammanfattning av nya riskidentifieringar.  
Den innehåller:

- Användare i riskzonen
- Misstänkta aktiviteter
- Identifierade säkerhetsproblem
- Länkar till relaterade rapporter i Identitetsskydd

![Veckovis sammandragsmeddelande](./media/howto-identity-protection-configure-notifications/400.png)

Som standard inkluderar mottagarna alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhetsadministratörer, säkerhetsläsare som mottagare.

### <a name="configure-weekly-digest-email"></a>Konfigurera veckovis sammanfattad e-post

Som administratör kan du aktivera eller inaktivera ett sammanfattande veckomeddelande och välja vilka användare som har tilldelats för att ta emot e-postmeddelandet.

Konfigurera veckosammanfattningsmeddelandet i **Azure-portalen** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Se även

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
