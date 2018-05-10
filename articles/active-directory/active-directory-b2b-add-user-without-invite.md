---
title: Lägga till B2B-samarbete användare i Azure Active Directory utan en inbjudan | Microsoft Docs
description: Du kan låta en gästanvändare lägga till andra gästanvändare i din Azure AD utan löser en inbjudan i Azure Active Directory B2B-samarbete.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 656651d067e5685aead65fdd647a0ea500ae7de9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Lägg till B2B-samarbete gästanvändare utan inbjudan

Du kan tillåta en användare som ett ombud för partner, lägga till användare från partnern i din organisation utan att behöva inbjudningar till lösas. Alla måste du göra är att bevilja användaren uppräkningen i katalogen som du använder för organisationen partner. 

Bevilja dessa behörighet när:

1. En användare i organisationen värden (till exempel WoodGrove) inbjuder en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden konfigurerar principer som gör att Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware).
3. Nu Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar till lösas. Om Sam har lämplig uppräkningen privilegier i Litware, det sker automatiskt.

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
- [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
- [Delegera inbjudningar för Azure Active Directory B2B-samarbete](active-directory-b2b-delegate-invitations.md)

