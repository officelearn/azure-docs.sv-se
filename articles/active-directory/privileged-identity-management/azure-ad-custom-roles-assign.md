---
title: Tilldela azure AD-anpassad roll - PRIVILEGIErad identitetshantering (PIM)
description: Så här tilldelar du en anpassad Azure AD-roll för PIM (Privilegierad identitetshantering)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499238"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Tilldela en anpassad Azure AD-roll i privilegierad identitetshantering

I den här artikeln beskrivs hur du använder PIM (Privileged Identity Management) för att skapa just-in-time och tidsbundna tilldelningar till anpassade roller som skapats för att hantera program i azure Active Directory (Azure AD) administrativ upplevelse.

- Mer information om hur du skapar anpassade roller för att delegera programhantering i Azure AD finns [i Anpassade administratörsroller i Azure Active Directory (förhandsversion).](../users-groups-roles/roles-custom-overview.md)
- Om du inte har använt Privilegierad identitetshantering ännu kan du få mer information när [du börjar använda Privilegierad identitetshantering](pim-getting-started.md).
- Information om hur du ger en annan administratör åtkomst till hantering av privilegierad identitetshantering finns i [Bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Azure AD-anpassade roller är inte integrerade med de inbyggda katalogrollerna under förhandsversionen. När kapaciteten är allmänt tillgänglig kommer rollhantering att ske i den inbyggda rollupplevelsen. Om du ser följande banner bör dessa roller hanteras [i den inbyggda rollupplevelsen](pim-how-to-activate-role.md) och den här artikeln gäller inte:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Tilldela en roll

Privilegierad identitetshantering kan hantera anpassade roller som du kan skapa i Azure Active Directory (Azure AD) programhantering.  Följande steg gör en kvalificerad tilldelning till en anpassad katalogroll.

1. Logga in [på privilegierad identitetshantering](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure-portalen med ett användarkonto som har tilldelats rollen Privilegierad rolladministratör.
1. Välj **anpassade Azure AD-roller (förhandsversion)**.

    ![Välj förhandsversion av anpassade roller för anpassade roller i Azure AD för att se kvalificerade rolltilldelningar](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Välj **Roller** om du vill visa en lista över anpassade roller för Azure AD-program.

    ![Välj Roller se listan över kvalificerade rolltilldelningar](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Välj **Lägg till medlem** om du vill öppna tilldelningssidan.
1. Om du vill begränsa omfattningen av rolltilldelningen till ett enda program väljer du **Scope** för att ange ett programomfång.

    ![begränsa omfattningen av kvalificerade rolltilldelningar i Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Välj **Välj en roll** om du vill öppna listan Välj en **roll.**

    ![välja den kvalificerade roll som ska tilldelas en användare](./media/azure-ad-custom-roles-assign/select-role.png)

1. Välj en roll som du vill tilldela och klicka sedan på **Markera**. Listan **Välj en medlem** öppnas.

    ![välja den användare som du tilldelar rollen](./media/azure-ad-custom-roles-assign/select-member.png)

1. Markera en användare som du vill tilldela rollen och klicka sedan på **Välj**. Listan **Inställningar för medlemskap** öppnas.

    ![Ange rolltilldelningstypen till kvalificerad eller aktiv](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. På sidan **Inställningar för medlemskap** väljer du **Kvalificerad** eller **Aktiv:**

    - **Kvalificerade** tilldelningar kräver att användaren som tilldelats rollen utför en åtgärd innan de kan använda rollen. Åtgärder kan omfatta att skicka en multifaktorautentiseringskontroll, tillhandahålla en affärsmotivering eller begära godkännande från utsedda godkännare.
    - **Aktiva** tilldelningar kräver inte att den tilldelade användaren utför någon åtgärd för att använda rollen. Aktiva användare har alltid de privilegier som tilldelats rollen.

1. Om kryssrutan **Permanent** är närvarande och tillgänglig (beroende på rollinställningar) kan du ange om tilldelningen är permanent. Markera kryssrutan om du vill göra tilldelningen permanent kvalificerad eller permanent tilldelad. Avmarkera kryssrutan om du vill ange en tilldelningstid.
1. Om du vill skapa den nya rolltilldelningen klickar du på **Spara** och sedan **lägga till**. Ett meddelande om tilldelningsprocessens status visas.

Om du vill verifiera rolltilldelningen väljer du **Tilldelningar** > **Tilldela** i en öppen roll och kontrollerar att rolltilldelningen är korrekt identifierad som kvalificerad eller aktiv.

 ![Kontrollera om rolltilldelningen är synlig som kvalificerad eller aktiv](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-configure.md)
- [Rolldefinitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
