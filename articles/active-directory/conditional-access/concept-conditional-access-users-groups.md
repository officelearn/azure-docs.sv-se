---
title: Användare och grupper i princip för villkorlig åtkomst – Azure Active Directory
description: Som är användare och grupper i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755279"
---
# <a name="conditional-access-users-and-groups"></a>Villkorlig åtkomst: användare och grupper

En princip för villkorlig åtkomst måste innehålla en användar tilldelning som en av signalerna i besluts processen. Användare kan tas med eller undantas från principer för villkorlig åtkomst. 

![Användare som en signal i besluten som fattas av villkorlig åtkomst](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Inkludera användare

Den här listan över användare inkluderar vanligt vis alla användare som en organisation är mål för i en princip för villkorlig åtkomst. 

Följande alternativ är tillgängliga för att ta med när du skapar en princip för villkorlig åtkomst.

- Inga
   - Inga användare har valts
- Alla användare
   - Alla användare som finns i katalogen, inklusive B2B-gäster.
- Välj användare och grupper
   - Alla gäst-och externa användare
      - Det här valet inkluderar alla B2B-gäster och externa användare, inklusive alla `user type` användare med attributet `guest`inställt på. Det här valet gäller även för alla externa användare som är inloggade från en annan organisation som en moln lösnings leverantör (CSP). 
   - Katalogroller
      - Administratörer kan välja särskilda Azure AD-katalog roller som används för att fastställa tilldelningen. Organisationer kan till exempel skapa en mer restriktiv princip för användare som har tilldelats rollen som global administratör.
   - Användare och grupper
      - Tillåter mål för specifika uppsättningar av användare. Organisationer kan till exempel välja en grupp som innehåller alla medlemmar i PERSONALAVDELNINGEN när en HR-app väljs som Cloud-App. En grupp kan vara vilken typ av grupp som helst i Azure AD, inklusive dynamiska eller tilldelade säkerhets-och distributions grupper.

## <a name="exclude-users"></a>Exkludera användare

När organisationer både inkluderar och exkluderar en användare eller grupp, undantas användaren eller gruppen från principen, eftersom en undantags åtgärd åsidosätter en inkludera i principen. Undantag används ofta för nöd åtkomst eller Bryt glass konton. Mer information om konton för nöd åtkomst och varför de är viktiga finns i följande artiklar: 

* [Hantera konton för nöd åtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Skapa en elastisk strategi för hantering av åtkomst kontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)

Följande alternativ är tillgängliga för att undanta när du skapar en princip för villkorlig åtkomst.

- Alla gäst-och externa användare
   - Det här valet inkluderar alla B2B-gäster och externa användare, inklusive alla `user type` användare med attributet `guest`inställt på. Det här valet gäller även för alla externa användare som är inloggade från en annan organisation som en moln lösnings leverantör (CSP). 
- Katalogroller
   - Administratörer kan välja särskilda Azure AD-katalog roller som används för att fastställa tilldelningen. Organisationer kan till exempel skapa en mer restriktiv princip för användare som har tilldelats rollen som global administratör.
- Användare och grupper
   - Tillåter mål för specifika uppsättningar av användare. Organisationer kan till exempel välja en grupp som innehåller alla medlemmar i PERSONALAVDELNINGEN när en HR-app väljs som Cloud-App. En grupp kan vara vilken typ av grupp som helst i Azure AD, inklusive dynamiska eller tilldelade säkerhets-och distributions grupper.

### <a name="preventing-administrator-lockout"></a>Förhindra administratörs utelåsning

För att förhindra att en administratör låser sig själva från sin katalog när du skapar en princip som tillämpas på **alla användare** och **alla appar**visas följande varning.

> Lås inte dig själv! Vi rekommenderar att du använder en princip för en liten uppsättning användare först för att kontrol lera att den fungerar som förväntat. Vi rekommenderar också att du undantar minst en administratör från den här principen. Detta säkerställer att du fortfarande har åtkomst och kan uppdatera en princip om en ändring krävs. Granska berörda användare och appar.

Som standard tillhandahåller principen ett alternativ för att undanta den aktuella användaren från principen, men denna standard kan åsidosättas av administratören som visas i följande bild. 

![Varning, Lås inte ut!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: molnappar eller åtgärder](concept-conditional-access-cloud-apps.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
