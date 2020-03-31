---
title: Lägg till B2B-gäster utan en inbjudningslänk eller e-post - Azure AD
description: Du kan låta en gästanvändare lägga till andra gästanvändare i din Azure AD utan att lösa in en inbjudan i Azure Active Directory B2B-samarbete.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050895"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Lägga till B2B-samarbetsgästanvändare utan en inbjudningslänk eller e-post

Nu kan du bjuda in gästanvändare genom att skicka ut en direktlänk till en delad app. Med den här metoden behöver gästanvändare inte längre använda e-postmeddelandet för inbjudan, förutom i vissa speciella fall. En gästanvändare klickar på applänken, granskar och accepterar sekretessvillkoren och kommer sedan sömlöst åt appen. Mer information finns i [B2B-inlösen av samarbetsinbjudan](redemption-experience.md).   

Innan den nya metoden var tillgänglig kan du bjuda in gästanvändare utan att kräva e-post via inbjudan genom att lägga till en inbjudningsfilt (från din organisation eller från en partnerorganisation) till katalogrollen **Gäst inbjudna** och sedan låta inbjudna användare lägga till gästanvändare i katalogen, grupperna eller programmen via användargränssnittet eller via PowerShell. (Om du använder PowerShell kan du undertrycka e-postmeddelandet med inbjudan helt och hållet). Ett exempel:

1. En användare i värdorganisationen (till exempel WoodGrove) bjuder in en Sam@litware.comanvändare från partnerorganisationen (till exempel ) som Gäst.
2. Administratören i värdorganisationen [ställer in principer](delegate-invitations.md) som gör att Sam kan identifiera och lägga till andra användare från partnerorganisationen (Litware). (Sam måste läggas till i rollen **Gäst inbjudna.)**
3. Nu kan Sam lägga till andra användare från Litware till WoodGrove-katalogen, grupperna eller programmen utan att behöva inbjudningar för att lösas in. Om Sam har rätt uppräkningsbehörighet i Litware sker den automatiskt.
 
Den här originalmetoden fungerar fortfarande. Men det finns en liten skillnad i beteende. Om du använder PowerShell kommer du att märka att ett inbjudet gästkonto nu har status **som väntande accepterad** i stället för att omedelbart visa **Accepterad**. Även om statusen väntar kan gästanvändaren fortfarande logga in och komma åt appen utan att klicka på en länk för e-postbjudan. Den väntande statusen innebär att användaren ännu inte har gått igenom [samtyckesupplevelsen](redemption-experience.md#consent-experience-for-the-guest), där de accepterar sekretessvillkoren för den inbjudande organisationen. Gästanvändaren ser den här medgivandeskärmen när de loggar in för första gången. 

Om du bjuder in en användare till katalogen måste gästanvändaren komma https://portal.azure.com/åt den resursklientspecifika Azure-portal-URL:en direkt (till exempel *resursansvarig*.onmicrosoft.com) för att visa och godkänna sekretessvillkoren.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [B2B samarbete inbjudan inlösen](redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](delegate-invitations.md)
- [Hur lägger informationsarbetare till B2B-samarbetsanvändare?](add-users-information-worker.md)

