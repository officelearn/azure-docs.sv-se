---
title: Delegera inbjudningar för Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete användaregenskaper kan konfigureras
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929315"
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

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Lägg till B2B-samarbete gästanvändare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
- [Lägger till en B2B-samarbete användare till en roll](active-directory-b2b-add-guest-to-role.md)


