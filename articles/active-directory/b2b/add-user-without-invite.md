---
title: Lägga till B2B-samarbete användare i Azure Active Directory utan en inbjudan | Microsoft Docs
description: Du kan låta en gästanvändare lägga till andra gästanvändare i din Azure AD utan löser en inbjudan i Azure Active Directory B2B-samarbete.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Lägg till B2B-samarbete gästanvändare utan inbjudan

> [!NOTE]
> Nu behöver gästanvändare inte längre e-postinbjudan utom i vissa särskilda fall. Mer information finns i [B2B-samarbete inbjudan inlösning](redemption-experience.md).  

Du kan tillåta en användare som ett ombud för partner, lägga till användare från partnern i din organisation utan att behöva inbjudningar till lösas. Alla måste du göra är att bevilja användaren uppräkningen i katalogen som du använder för organisationen partner. 

Bevilja dessa behörighet när:

1. En användare i organisationen värden (till exempel WoodGrove) inbjuder en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden [ställer in principer](delegate-invitations.md) som tillåter Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware).
3. Nu Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar till lösas. Om Sam har lämplig uppräkningen privilegier i Litware, det sker automatiskt.

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](add-users-information-worker.md)
- [B2B-samarbete inbjudan inlösning](redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](delegate-invitations.md)

