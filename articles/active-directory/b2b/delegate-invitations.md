---
title: Aktivera inställningar för externt samarbete i B2B – Azure AD
description: Lär dig hur du aktiverar Active Directory B2B externt samarbete och hanterar vem som kan bjuda in gäst användare. Använd rollen gäst deltagare för att delegera inbjudningar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272906"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster

I den här artikeln beskrivs hur du aktiverar Azure Active Directory (Azure AD) B2B-samarbete och avgör vem som kan bjuda in gäster. Som standard kan alla användare och gäster i din katalog bjuda in gäster även om de inte har tilldelats någon administratörs roll. Med inställningar för externa samarbete kan du aktivera eller inaktivera gäst inbjudningar för olika typer av användare i din organisation. Du kan också delegera inbjudningar till enskilda användare genom att tilldela roller som gör det möjligt för dem att bjuda in gäster.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete i B2B

Med Azure AD B2B-samarbete kan en klient organisations administratör ange följande Inbjudnings principer:

- Inaktivera inbjudningar
- Endast administratörer och användare i rollen gäst deltagare kan bjuda in
- Administratörer, rollen gäst deltagare och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Som standard kan alla användare, inklusive gäster, bjuda in gäst användare.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete:

1. Logga in på [Azure Portal](https://portal.azure.com) som klient organisations administratör.
2. Välj **Azure Active Directory** > **användare** > **användar inställningar**.
3. Under **externa användare**väljer du **Hantera inställningar för externt samarbete**.
   > [!NOTE]
   > De **externa samarbets inställningarna** är också tillgängliga på sidan **organisations relationer** . I Azure Active Directory, under **Hantera**, går du till **organisations relationer** > **Inställningar**.
4. På sidan **Inställningar för externt samarbete** väljer du de principer som du vill aktivera.

   ![Inställningar för externt samarbete](./media/delegate-invitations/control-who-to-invite.png)

  - **Gäst användarens behörigheter är begränsade**: den här principen fastställer behörigheter för gäster i din katalog. Välj **Ja** om du vill blockera gäster från vissa katalog aktiviteter, t. ex. att räkna upp användare, grupper eller andra katalog resurser. Välj **Nej** om du vill ge gästerna samma åtkomst till katalog data som vanliga användare i din katalog.
   - **Administratörer och användare i rollen gäst deltagare kan bjuda in**: om du vill tillåta administratörer och användare i rollen "gäst inbjudare" att bjuda in gäster, ställer du in den här principen på **Ja**.
   - **Medlemmar kan bjuda in**: om du vill tillåta icke-administratörers medlemmar i katalogen att bjuda in gäster, ställer du in den här principen på **Ja**.
   - **Gäster kan bjuda in**: om du vill tillåta gäster att bjuda in andra gäster ställer du in den här principen på **Ja**.
   - **Aktivera e-post med eng ång slö sen ord för gäster (för hands version)** : Mer information om funktionen för eng ång slö sen ord finns i [e-mail Authentication eng ång slö sen ord (för hands version)](one-time-passcode.md).
   - **Samarbets begränsningar**: Mer information om att tillåta eller blockera inbjudningar till vissa domäner finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Tilldela rollen för rollen gäst deltagare till en användare

Med rollen gäst deltagare kan du ge enskilda användare möjlighet att bjuda in gäster utan att tilldela dem en global administratör eller någon annan administratörs roll. Tilldela rollen för rollen gäst deltagare till enskilda användare. Kontrol lera sedan att du anger **Administratörer och användare i rollen gäst deltagare kan bjuda in** till **Ja**.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare i rollen som gäst inbjudare:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till gäst användare för B2B-samarbete utan inbjudan](add-user-without-invite.md)
- [Lägga till en B2B-samarbets användare till en roll](add-guest-to-role.md)


