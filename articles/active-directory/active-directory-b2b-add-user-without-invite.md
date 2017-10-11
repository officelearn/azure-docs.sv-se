---
title: "Lägga till B2B-samarbete användare i Azure Active Directory utan en inbjudan | Microsoft Docs"
description: "Du kan låta en gästanvändare lägga till andra gästanvändare i din Azure AD utan löser en inbjudan i Azure Active Directory B2B-samarbete."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 91b9477cdb679851e7d8d2942c06999a05f64e46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Lägg till B2B-samarbete gästanvändare utan inbjudan

Du kan tillåta en användare som ett ombud för partner, lägga till användare från partnern i din organisation utan att behöva inbjudningar till lösas. Alla måste du göra är att bevilja användaren uppräkningen i katalogen som du använder för organisationen partner. 

Bevilja dessa behörighet när:

1. En användare i organisationen värden (till exempel WoodGrove) inbjuder en användare från partnerorganisationen (till exempel Sam@litware.com) som gäst.
2. Administratören i organisationen värden konfigurerar principer som gör att Sam att identifiera och lägga till andra användare från partnerorganisationen (Litware).
3. Nu Sam kan lägga till andra användare från Litware WoodGrove directory, grupper eller program utan att behöva inbjudningar till lösas. Om Sam har lämplig uppräkningen privilegier i Litware, det sker automatiskt.

### <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-samarbete API och anpassning](active-directory-b2b-api.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)