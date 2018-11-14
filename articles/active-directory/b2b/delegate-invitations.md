---
title: Delegera inbjudningar för Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete användaregenskaper kan konfigureras
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fd00fb8da3cf36518f9e28e827e59fd7ff45d687
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622218"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegera inbjudningar för Azure Active Directory B2B-samarbete

Med Azure Active Directory (Azure AD) business-to-business (B2B) samarbete behöver inte vara en global administratör för att skicka inbjudningar. Du kan i stället använda principer och delegera inbjudningar till användare vars roller att de kan skicka inbjudningar. Det är ett viktigt nytt sätt att delegera inbjudningar för gästoperativsystem via rollen Gästinbjudare.

## <a name="guest-inviter-role"></a>Gäst bjuder in roll
Vi kan tilldela användaren till rollen Gästinbjudare att skicka inbjudningar. Du behöver inte vara medlem i rollen global administratör för att skicka inbjudningar. Som standard anropa vanliga användare API: et för inbjudan, såvida inte en global administratör har inaktiverat inbjudningar för vanliga användare. En användare kan även anropa API: et med Azure-portalen eller PowerShell.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare till rollen Gästinbjudare:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Kontrollera vem som kan bjuda in

Gå till Azure Active Directory > Inställningar > Hantera inställningar för externt samarbete

![externalusers](https://user-images.githubusercontent.com/13383753/45905128-2c47f680-bda4-11e8-955d-6219c67935e0.PNG)

Med Azure AD B2B-samarbete, Innehavaradministratör ställa in inbjudan följande principer:

- Inaktivera inbjudningar
- Bara administratörer och användare i rollen Gästinbjudare kan bjuda in
- Administratörer, Gäst bjuder in roll och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Klienter är som standard #4. (Alla användare, inklusive gäster, kan bjuda in B2B-användare.)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägga till gästanvändare för B2B-samarbete utan inbjudan](add-user-without-invite.md)
- [Att lägga till en B2B-användare till en roll](add-guest-to-role.md)


