---
title: Användare och grupper i principen villkorlig åtkomst - Azure Active Directory
description: Vilka är användare och grupper i en Azure AD-princip för villkorlig åtkomst
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43ae866959dd2112bacbb6b56e5683e7b3b851a0
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631936"
---
# <a name="conditional-access-users-and-groups"></a>Villkorlig åtkomst: Användare och grupper

En princip för villkorlig åtkomst måste innehålla en användartilldelning som en av signalerna i beslutsprocessen. Användare kan inkluderas eller uteslutas från principer för villkorlig åtkomst. 

![Användaren som en signal i de beslut som fattas av villkorlig åtkomst](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Inkludera användare

Den här listan över användare innehåller vanligtvis alla användare som en organisation riktar in sig på i en princip för villkorlig åtkomst. 

Följande alternativ är tillgängliga när du skapar en princip för villkorlig åtkomst.

- Inget
   - Inga användare markerade
- Alla användare
   - Alla användare som finns i katalogen inklusive B2B-gäster.
- Välj användare och grupper
   - Alla gäst- och externa användare (förhandsgranskning)
      - Det här valet omfattar alla B2B-gäster `user type` och externa `guest`användare, inklusive alla användare med attributet inställt på . Det här valet gäller även för alla externa användare som är inloggade från en annan organisation som en CSP (Cloud Solution Provider). 
   - Katalogroller (förhandsgranskning)
      - Tillåter administratörer att välja specifika Azure AD-katalogroller som används för att bestämma tilldelning. Organisationer kan till exempel skapa en mer restriktiv princip för användare som tilldelats den globala administratörsrollen.
   - Användare och grupper
      - Tillåter inriktning på specifika användaruppsättningar. Organisationer kan till exempel välja en grupp som innehåller alla medlemmar i HR-avdelningen när en HR-app väljs som molnapp. En grupp kan vara vilken typ av grupp som helst i Azure AD, inklusive dynamiska eller tilldelade säkerhets- och distributionsgrupper.

## <a name="exclude-users"></a>Exkludera användare

När organisationer både inkluderar och utesluter en användare eller grupp utesluts användaren eller gruppen från principen, eftersom en undantagsåtgärd åsidosätter en inkludera i principen. Undantag används ofta för nödåtkomst- eller glaskonton. Mer information om konton för nödåtkomst och varför de är viktiga finns i följande artiklar: 

* [Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Skapa en strategi för hantering av flexibel åtkomstkontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)

Följande alternativ är tillgängliga att utesluta när du skapar en princip för villkorlig åtkomst.

- Alla gäst- och externa användare (förhandsgranskning)
   - Det här valet omfattar alla B2B-gäster `user type` och externa `guest`användare, inklusive alla användare med attributet inställt på . Det här valet gäller även för alla externa användare som är inloggade från en annan organisation som en CSP (Cloud Solution Provider). 
- Katalogroller (förhandsgranskning)
   - Tillåter administratörer att välja specifika Azure AD-katalogroller som används för att bestämma tilldelning. Organisationer kan till exempel skapa en mer restriktiv princip för användare som tilldelats den globala administratörsrollen.
- Användare och grupper
   - Tillåter inriktning på specifika användaruppsättningar. Organisationer kan till exempel välja en grupp som innehåller alla medlemmar i HR-avdelningen när en HR-app väljs som molnapp. En grupp kan vara vilken typ av grupp som helst i Azure AD, inklusive dynamiska eller tilldelade säkerhets- och distributionsgrupper.

### <a name="preventing-administrator-lockout"></a>Förhindra administratörsutelåsning

Om du vill hindra en administratör från att låsa sig utanför sin katalog när han eller hon skapar en princip som tillämpas på **alla användare** och **alla appar**visas följande varning.

> Lås inte ut dig själv! Vi rekommenderar att du använder en princip på en liten uppsättning användare först för att verifiera att den fungerar som förväntat. Vi rekommenderar också att minst en administratör utesluts från den här principen. Detta säkerställer att du fortfarande har åtkomst och kan uppdatera en princip om en ändring krävs. Läs de berörda användarna och apparna.

Som standard ger principen ett alternativ för att utesluta den aktuella användaren från principen, men den här standardinställningen kan åsidosättas av administratören enligt följande bild. 

![Varning, lås inte ut dig själv!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: Molnappar eller -åtgärder](concept-conditional-access-cloud-apps.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
