---
title: Aktivera B2B-inställningar för externt samarbete – Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar Active Directory B2B externt samarbete och hantera vem som kan bjuda in gästanvändare. Använda rollen Gästinbjudare för att delegera inbjudningar.
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
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812674"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Aktivera externa B2B-samarbete och hantera vem som kan bjuda in gäster

Den här artikeln beskriver hur du aktiverar Azure Active Directory (Azure AD) B2B-samarbete och bestämma vem som kan bjuda in gäster. Som standard alla användare och gäster i din katalog kan bjuda in gäster även om de inte har tilldelats en administratörsroll. Inställningar för externt samarbete kan du förvandla gäst inbjudningar eller inaktivera för olika typer av användare i din organisation. Du kan också delegera inbjudningar till enskilda användare genom att tilldela roller att de kan bjuda in gäster.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurera inställningar för B2B-externt samarbete

Med Azure AD B2B-samarbete, Innehavaradministratör ställa in inbjudan följande principer:

- Inaktivera inbjudningar
- Bara administratörer och användare i rollen Gästinbjudare kan bjuda in
- Administratörer, Gäst bjuder in roll och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Som standard kan alla användare, inklusive gäster, bjuda in gästanvändare.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som en Innehavaradministratör.
2. Välj **Azure Active Directory** > **användare** > **användarinställningar**.
3. Under **externa användare**väljer **hantera inställningar för externt samarbete**.
   > [!NOTE]
   > Den **inställningar för externt samarbete** är också tillgängliga från den **organisationens relationer** sidan. I Azure Active Directory, under **hantera**går du till **organisationens relationer** > **inställningar**.
4. På den **inställningar för externt samarbete** väljer du de principer som du vill aktivera.

   ![Inställningar för externt samarbete](./media/delegate-invitations/control-who-to-invite.png)

  - **Behörigheterna för gästanvändare är begränsade**: Den här principen anger behörigheter för gäster i din katalog. Välj **Ja** att blockera gäster från vissa directory aktiviteter, till exempel räkna upp användare, grupper eller andra katalogresurser. Välj **nr** att ge gäster samma åtkomst till katalogdata som vanliga användare i din katalog.
   - **Administratörer och användare i gästinbjudarrollen kan bjuda in**: För att tillåta administratörer och användare i rollen ”gäst bjuder in” för att bjuda in gäster, ställa in principen **Ja**.
   - **Medlemmar kan bjuda in**: Om du vill tillåta icke-administratörer medlemmar i din katalog att bjuda in gäster att ställa in principen **Ja**.
   - **Gäster kan bjuda in**: Om du vill tillåta gäster att bjuda in andra gäster för att ställa in principen **Ja**.
   - **Aktivera e-engångslösenord lösenord för gäster (förhandsversion)** : Läs mer om funktionen engångskod [e-post engångskod authentication (förhandsversion)](one-time-passcode.md).
   - **Samarbetesbegränsningar**: Läs mer om att tillåta eller blockera inbjudningar till vissa domäner [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Tilldela rollen Gästinbjudare till en användare

Du kan göra kan bjuda in gäster utan att tilldela dem en global administratör eller andra administratörsroll enskilda användare med rollen Gästinbjudare. Tilldela enskilda användare i rollen som gästinbjudare. Se till att ange **administratörer och användare i gästinbjudarrollen kan bjuda in** till **Ja**.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare till rollen Gästinbjudare:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägga till gästanvändare för B2B-samarbete utan inbjudan](add-user-without-invite.md)
- [Att lägga till en B2B-användare till en roll](add-guest-to-role.md)


