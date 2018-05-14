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
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Lägg till B2B-samarbete gästanvändare utan inbjudan

> [!NOTE]
> Nu behöver gästanvändare inte längre e-postinbjudan utom i vissa särskilda fall. Mer information finns i [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md).  

Du kan tillåta en användare som ett ombud för partner, lägga till användare från partnern i din organisation utan att behöva inbjudningar till lösas. Alla måste du göra är att bevilja användaren uppräkningen i katalogen som du använder för organisationen partner. 

Bevilja dessa behörighet när:

1. En användare i organisationen värden (till exempel WoodGrove) inbjuder en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden [ställer in principer](active-directory-b2b-delegate-invitations.md) som tillåter Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware).
3. Nu Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar till lösas. Om Sam har lämplig uppräkningen privilegier i Litware, det sker automatiskt.

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
- [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](active-directory-b2b-delegate-invitations.md)

