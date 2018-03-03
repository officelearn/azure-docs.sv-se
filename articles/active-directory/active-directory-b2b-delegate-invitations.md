---
title: "Delegera inbjudningar för Azure Active Directory B2B-samarbete | Microsoft Docs"
description: "Azure Active Directory B2B-samarbete användaregenskaper kan konfigureras"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: facf0f62823c84742986c9fb585990d7fedb2ab1
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegera inbjudningar för Azure Active Directory B2B-samarbete

Du behöver inte vara en global administratör för att skicka inbjudningar med Azure Active Directory (AD Azure) business-to-business (B2B) samarbete. Du kan i stället använda principer och delegera inbjudningar till användare vars roller tillåta dem att skicka inbjudningar. Det är ett viktigt nytt sätt att delegera gästen användaren inbjudningar via rollen gäst bjuder in.

## <a name="guest-inviter-role"></a>Gästen bjuder in roll
Vi kan tilldela användaren rollen gäst bjuder in skicka inbjudningar. Du behöver inte vara medlem i rollen global administratör för att skicka inbjudningar. Som standard anropa vanliga användare API för inbjudan en global administratör inaktiverat inbjudningar för vanliga användare. En användare kan även anropa API: et med Azure-portalen eller PowerShell.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare i rollen gäst bjuder in:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Kontrollera vem som kan bjuda in

![Styra hur att bjuda in](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

En klientadministratör kan ange följande inbjudan principer med Azure AD B2B-samarbete:

- Inaktivera inbjudningar
- Bara administratörer och användare med rollen gäst bjuder in bjuda in
- Administratörer, Gäst bjuder in roll och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Klienter är som standard #4. (Alla användare, inklusive gäster, erbjuda B2B-användare.)

## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Egenskaper för användare av B2B-samarbete](active-directory-b2b-user-properties.md)
* [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)
* [Dynamiska grupper och B2B-samarbete](active-directory-b2b-dynamic-groups.md)
* [B2B-samarbete kod och PowerShell-exempel](active-directory-b2b-code-samples.md)
* [Konfigurera SaaS-appar för B2B-samarbete](active-directory-b2b-configure-saas-apps.md)
* [Användartoken för B2B-samarbete](active-directory-b2b-user-token.md)
* [B2B-samarbete användaranspråk mappning](active-directory-b2b-claims-mapping.md)
* [Extern delning av Office 365](active-directory-b2b-o365-external-user.md)
* [Aktuella begränsningar för B2B-samarbete](active-directory-b2b-current-limitations.md)
