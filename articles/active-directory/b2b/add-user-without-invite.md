---
title: Lägga till B2B-gäster utan en inbjudan länk eller e-post – Azure Active Directory | Microsoft Docs
description: Du kan låta en gästanvändare lägga till andra gästanvändare till din Azure AD utan löser in inbjudan i Azure Active Directory B2B-samarbete.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81aad3ef9a4a53532d19fdb81bc48fc50931d49c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056057"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Lägga till gästanvändare för B2B-samarbete utan en inbjudan länk eller e-post

Du kan nu bjuda in gästanvändare genom att skicka ut en direktlänk till en delad app. Med den här metoden behöver inte längre gästanvändare rätt att använda e-postinbjudan utom i vissa särskilda fall. En gästanvändare klickar på länken för app, granskar och accepterar sekretesspolicyn och sedan har åtkomst till smidigt appen. Mer information finns i [inlösning av inbjudan för B2B-samarbete](redemption-experience.md).   

Innan den nya metoden var tillgänglig, kan du bjuda in gästanvändare utan e-postinbjudan genom att lägga till en inbjudaren (från din organisation eller från en partnerorganisation) till den **gästinbjudare** katalogroll, och sedan med avsändaren lägga till gästanvändare i katalogen, grupper eller program via Användargränssnittet eller PowerShell. (Om du använder PowerShell, du kan förhindra e-postinbjudan helt och hållet). Exempel:

1. En användare i organisationen (till exempel WoodGrove) värden bjuder in en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden [ställer in principer](delegate-invitations.md) som gör det Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware). (Sam måste läggas till i **gästinbjudare** roll.)
3. Nu kan Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar lösas. Om Sam har behörigheter som lämplig uppräkning i Litware, sker det automatiskt.
 
Den här ursprungliga metoden fortfarande fungerar. Det finns dock små skillnader i beteende. Om du använder PowerShell, märker du att en inbjudna gästkontot har nu en **PendingAcceptance** status i stället för direkt som visar **godkända**. Även om statusen är pågående, gästanvändaren fortfarande logga in och komma åt appen utan att klicka på en e-postinbjudan länk. Väntande status innebär att användaren inte har ännu har genomgått den [godkänna upplevelse](redemption-experience.md#consent-experience-for-the-guest), där de accepterar villkoren i organisationen som bjuder in sekretess. Gästanvändaren ser den här godkännandeskärmen när de loggar in för första gången. 

Om du bjuda in användare till katalogen gästanvändaren måste komma åt resursen klientspecifik Azure-portalen URL direkt (till exempel https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) att visa och samtycker till sekretesspolicyn.

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [B2B-samarbete inlösning av inbjudan](redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](delegate-invitations.md)
- [Hur lägger informationsarbetare till användare i B2B-samarbetet?](add-users-information-worker.md)

