---
title: Lägg till B2B-gäster utan en Inbjudnings länk eller e-post – Azure AD
description: Du kan låta en gäst användare lägga till andra gäst användare i Azure AD utan att lösa in en inbjudan i Azure Active Directory B2B-samarbete.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: cab29fbe9e33cc12336fa1b81ed4aa37f91a7f5f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387311"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Lägga till gäst användare för B2B-samarbete utan någon inbjudan eller e-post

Nu kan du bjuda in gäst användare genom att skicka ut en direkt länk till en delad app. Med den här metoden behöver gäst användare inte längre använda inbjudan via e-post, förutom i vissa specialfall. En gäst användare klickar på appens länk, granskar och godkänner sekretess villkoren och ansluter sedan sömlöst till appen. Mer information finns i [B2B-samverkan med inbjudan](redemption-experience.md).   

Innan den här nya metoden var tillgänglig kan du bjuda in gäst användare utan att behöva skicka inbjudan via e-post genom att lägga till en inbjuder (från din organisation eller från en partner organisation) till katalog rollen för **gäst inbjudningar** och sedan låta den inbjudna lägga till gäst användare i katalogen, grupper eller program via användar gränssnittet eller PowerShell. (Om du använder PowerShell kan du utelämna inbjudans e-postadress helt). Ett exempel:

1. En användare i värd organisationen (till exempel sparbank) bjuder in en användare från partner organisationen (till exempel Sam@litware.com ) som gäst.
2. Administratören i värd organisationen ställer in [principer](delegate-invitations.md) som gör det möjligt för Sam att identifiera och lägga till andra användare från partner organisationen (Litware '). (Sam måste läggas till i rollen för **gäst inbjudningar** .)
3. Nu kan Sam lägga till andra användare från Litware ' till katalogen sparbanker, grupper eller program utan att behöva göra några inbjudningar. Om Sam har rätt uppräknings privilegier i Litware ' sker det automatiskt.
 
Den här ursprungliga metoden fungerar fortfarande. Det finns dock en liten skillnad i beteendet. Om du använder PowerShell ser du att ett ansvars bara gäst konto har statusen **PendingAcceptance** i stället för att omedelbart visa **accepterad**. Även om statusen är väntande kan gäst användaren fortfarande logga in och få åtkomst till appen utan att klicka på länken e-postinbjudan. Väntande status innebär att användaren ännu inte har gått igenom [medgivande processen](redemption-experience.md#consent-experience-for-the-guest), där de accepterar sekretess villkoren i den bjudande organisationen. Gäst användaren ser den här medgivande skärmen när de loggar in för första gången. 

Om du bjuder in en användare till katalogen måste gäst användaren komma åt den resursbaserade Azure Portal URL: en direkt (till exempel https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) för att visa och godkänna sekretess villkoren.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Inlösen av B2B-samarbets inbjudningar](redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](delegate-invitations.md)
- [Hur lägger informations anställda till B2B-samarbets användare?](add-users-information-worker.md)

