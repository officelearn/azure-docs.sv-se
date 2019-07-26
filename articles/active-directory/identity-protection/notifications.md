---
title: Azure Active Directory Identity Protection meddelanden | Microsoft Docs
description: Lär dig hur meddelanden stöder dina undersöknings aktiviteter.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335365"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meddelanden

Azure AD Identity Protection skickar två typer av automatiserade e-postmeddelanden som hjälper dig att hantera användar risker och risk händelser:

- Användare med skadligt upptäckt e-post
- E-postmeddelande om veckovis sammandrag

Den här artikeln ger en översikt över både e-postaviseringar.

## <a name="users-at-risk-detected-email"></a>Användare med skadligt upptäckt e-post

Som svar på ett identifierat konto vid risk genererar Azure AD Identity Protection en e-postavisering med **användare som riskerar att identifieras** som ämne. E-postmeddelandet innehåller en länk till de användare som har **[flaggats för risk](../reports-monitoring/concept-user-at-risk.md)** rapporten. Som bästa praxis bör du omedelbart undersöka de användare som är utsatta för risk.

Med konfigurationen för den här aviseringen kan du ange på vilken nivå för användar risk du vill att aviseringen ska genereras. E-postmeddelandet skapas när användarens risk nivå når det du har angett. du får dock inga nya användare när de riskerar att identifiera e-postaviseringar för den här användaren när de har flyttat till den här användar risk nivån. Om du t. ex. ställer in principen för att varna om medelhög användar risk och din användare John flyttar till medelhög risk, får användarna ett hot som upptäckts via e-post för John. Men du kommer inte att få en andra användare vid risk identifiering om John sedan flyttar till hög risk eller har ytterligare risk händelser.

![Användare med skadligt upptäckt e-post](./media/notifications/01.png)

### <a name="configuration"></a>Konfiguration

Som administratör kan du ange:

- **Den användar risk nivå som utlöser genereringen av det här e** -postmeddelandet som standard är risk nivån inställd på "hög" risk.
- Mottagare **av det här e** -postmeddelandet är som standard alla globala administratörer. Globala administratörer kan också lägga till andra globala administratörer, säkerhets administratörer och säkerhets läsare som mottagare.  

Öppna dialog rutan relaterad genom att klicka  på aviseringar i avsnittet **Inställningar** på sidan för **identitets skydd** .

![Användare med skadligt upptäckt e-post](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>E-postmeddelande om veckovis sammandrag

E-postmeddelandet om veckovis sammandrag innehåller en sammanfattning av nya risk händelser.  
Den innehåller:

- Användare i farozonen
- Misstänkta aktiviteter
- Identifierade sårbarheter
- Länkar till relaterade rapporter i Identity Protection

    ![Reparation](./media/notifications/400.png "Reparation")

### <a name="configuration"></a>Konfiguration

Som administratör kan du växla att skicka ett e-postmeddelande om veckovis Sammanfattning.

![Användar risker](./media/notifications/62.png "Användar risker")

Öppna den relaterade dialog rutan genom att klicka på **veckovis Sammanfattning** i avsnittet **Inställningar** på sidan för **identitets skydd** .

![Användare med skadligt upptäckt e-post](./media/notifications/04.png)

## <a name="see-also"></a>Se också

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
