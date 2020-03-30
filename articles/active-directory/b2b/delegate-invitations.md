---
title: Aktivera B2B-inställningar för externt samarbete - Azure AD
description: Lär dig hur du aktiverar externt samarbete i Active Directory B2B och hanterar vem som kan bjuda in gästanvändare. Använd rollen Gäst inbjudna att delegera inbjudningar.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272906"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Aktivera B2B externt samarbete och hantera vem som kan bjuda in gäster

I den här artikeln beskrivs hur du aktiverar Azure Active Directory (Azure AD) B2B-samarbete och avgör vem som kan bjuda in gäster. Som standard kan alla användare och gäster i katalogen bjuda in gäster även om de inte har tilldelats en administratörsroll. Med externa samarbetsinställningar kan du aktivera eller inaktivera gäst inbjudningar för olika typer av användare i organisationen. Du kan också delegera inbjudningar till enskilda användare genom att tilldela roller som gör att de kan bjuda in gäster.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete i B2B

Med Azure AD B2B-samarbete kan en klientadministratör ange följande inbjudningsprinciper:

- Inaktivera inbjudningar
- Endast administratörer och användare i rollen Gäst inbjudna kan bjuda in
- Administratörer, rollen Gäst inbjudna och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Som standard kan alla användare, inklusive gäster, bjuda in gästanvändare.

### <a name="to-configure-external-collaboration-settings"></a>Så här konfigurerar du inställningar för externt samarbete:

1. Logga in på [Azure-portalen](https://portal.azure.com) som klientadministratör.
2. Välj**Användarinställningar för** **Azure Active Directory** > **-användare** > .
3. Under **Externa användare**väljer du Hantera inställningar för **externt samarbete**.
   > [!NOTE]
   > **Inställningarna för externt samarbete** är också tillgängliga på sidan **Organisationsrelationer.** Gå till Inställningar för **organisationsrelationer** >  **i**Azure Active**Directory.**
4. På sidan **Inställningar för externt samarbete** väljer du de principer som du vill aktivera.

   ![Inställningar för externt samarbete](./media/delegate-invitations/control-who-to-invite.png)

  - **Gästanvändarbehörigheterna är begränsade**: Den här principen bestämmer behörigheter för gäster i katalogen. Välj **Ja** om du vill blockera gäster från vissa kataloguppgifter, till exempel räkna upp användare, grupper eller andra katalogresurser. Välj **Nej** om du vill ge gästerna samma åtkomst till katalogdata som vanliga användare i katalogen.
   - **Administratörer och användare i rollen gäst inbjudna kan bjuda in:** Om du vill att administratörer och användare i rollen "Gäst inbjudna" ska kunna bjuda in gäster anger du den här policyn till **Ja**.
   - **Medlemmar kan bjuda in:** Om du vill att icke-administratörsmedlemmar i katalogen ska kunna bjuda in gäster ställer du in den här policyn på **Ja**.
   - **Gäster kan bjuda in:** Så att gästerna kan bjuda in andra gäster ställer du in policyn på **Ja**.
   - **Aktivera lösenord för engångslösenord för gäster (förhandsversion):** Mer information om engångslösenordsfunktionen finns i [E-postautentisering (förhandsgranskning) e-post](one-time-passcode.md).
   - **Samarbetsbegränsningar**: Mer information om hur du tillåter eller blockerar inbjudningar till specifika domäner finns i [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Tilldela rollen Gäst inbjudna till en användare

Med rollen Gäst inbjudna kan du ge enskilda användare möjlighet att bjuda in gäster utan att tilldela dem en global administratör eller annan administratörsroll. Tilldela rollen Gäst inbjudna till enskilda personer. Se sedan till att du anger **administratörer och användare i gäst inbjudna roll kan bjuda in** till **Ja**.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare i rollen Gästintrinatör:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägga till B2B-samarbetsgästanvändare utan inbjudan](add-user-without-invite.md)
- [Lägga till en B2B-samarbetsanvändare i en roll](add-guest-to-role.md)


