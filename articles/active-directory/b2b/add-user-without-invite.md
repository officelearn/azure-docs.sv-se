---
title: Lägga till B2B-samarbete användare i Azure Active Directory utan en inbjudan | Microsoft Docs
description: Du kan låta en gästanvändare lägga till andra gästanvändare i din Azure AD utan löser en inbjudan i Azure Active Directory B2B-samarbete.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/21/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 50c64386f1eab07c299112617283b1d8d7295295
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591059"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Lägg till B2B-samarbete gästanvändare utan inbjudan

Du kan nu bjuda in gästanvändare genom att skicka ut en direktlänk till en delad app. Med den här metoden gästanvändare inte längre behöver använda e-postinbjudan utom i vissa särskilda fall. Klickar du på länken app gästanvändare går igenom och godkänner villkoren sekretess och sedan har åtkomst till sömlöst appen. Mer information finns i [B2B-samarbete inbjudan inlösning](redemption-experience.md).   

Innan den här nya metoden var tillgänglig, kan du bjuda in gästanvändare utan e-postinbjudan genom att lägga till en bjuder in (från din organisation eller från en partnerorganisation) den **gäst bjuder in** directory roll, och sedan med avsändaren av inbjudan lägga till gästanvändare i katalogen, grupper eller program via Användargränssnittet eller PowerShell. (Om du använder PowerShell, du kan blockera e-postinbjudan helt). Exempel:

1. En användare i organisationen värden (till exempel WoodGrove) inbjuder en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden [ställer in principer](delegate-invitations.md) som tillåter Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware). (Sam måste läggas till i **gäst bjuder in** roll.)
3. Nu Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar till lösas. Om Sam har lämplig uppräkningen privilegier i Litware, det sker automatiskt.
 
Ursprungliga metoden fortfarande fungerar. Det är dock mindre skillnader i beteende. Om du använder PowerShell, märker du att en inbjudna gästkontot nu har en **PendingAcceptance** status i stället för direkt visar **godkända**. Även om statusen är pågående gästanvändaren fortfarande logga in och komma åt appen utan att klicka på en länk för e-inbjudan. Väntande status innebär att användaren inte har ännu har genomgått den [medgivande upplevelse](redemption-experience.md#privacy-policy-agreement), där de accepterar villkoren sekretess bjuda in organisationens. Gästanvändaren ser den här skärmen medgivande när de loggar in för första gången. 

Om du bjuda in användare i katalogen gästanvändaren måste komma åt resursen klient-specifika Azure-portalen direkt URL (t.ex https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) visar och godkänner villkoren sekretess.

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [B2B-samarbete inbjudan inlösning](redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](delegate-invitations.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](add-users-information-worker.md)

