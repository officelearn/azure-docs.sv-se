---
title: Uppdatera eller ta bort azure AD-anpassad roll – PRIVILEGIErad identitetshantering (PIM)
description: Så här uppdaterar eller tar du bort en anpassad Azure AD-rolltilldelning för privilegierad identitetshantering (PIM)
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
ms.openlocfilehash: ad5747be47e250cf9c623cc40d21d12c91ee16f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499107"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Uppdatera eller ta bort en tilldelad azure AD-anpassad roll i privilegierad identitetshantering

I den här artikeln beskrivs hur du använder PIM (Privileged Identity Management) för att uppdatera eller ta bort just-in-time- och tidsbunden tilldelning till anpassade roller som skapats för programhantering i azure Active Directory (Azure AD) administrativ upplevelse. 

- Mer information om hur du skapar anpassade roller för att delegera programhantering i Azure AD finns [i Anpassade administratörsroller i Azure Active Directory (förhandsversion).](../users-groups-roles/roles-custom-overview.md) 
- Om du inte har använt Privilegierad identitetshantering ännu kan du få mer information när [du börjar använda Privilegierad identitetshantering](pim-getting-started.md).

> [!NOTE]
> Azure AD-anpassade roller är inte integrerade med de inbyggda katalogrollerna under förhandsversionen. När kapaciteten är allmänt tillgänglig kommer rollhantering att ske i den inbyggda rollupplevelsen. Om du ser följande banner bör dessa roller hanteras [i den inbyggda rollupplevelsen](pim-how-to-add-role-to-user.md) och den här artikeln gäller inte:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Uppdatera eller ta bort en tilldelning

Följ dessa steg för att uppdatera eller ta bort en befintlig anpassad rolltilldelning.

1. Logga in [på privilegierad identitetshantering](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure-portalen med ett användarkonto som har tilldelats rollen Privilegierad rolladministratör.
1. Välj **anpassade Azure AD-roller (förhandsversion)**.

    ![Välj förhandsversion av anpassade roller för anpassade roller i Azure AD för att se kvalificerade rolltilldelningar](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Välj **Roller** om du vill visa listan **över tilldelningar** med anpassade roller för Azure AD-program.

    ![Välj Roller se listan över kvalificerade rolltilldelningar](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Välj den roll som du vill uppdatera eller ta bort.
1. Hitta rolltilldelningen på flikarna **Kvalificerade roller** eller **Aktiva roller.**
1. Välj **Uppdatera** eller **Ta bort** om du vill uppdatera eller ta bort rolltilldelningen.

    ![Välj ta bort eller uppdatera i den kvalificerade rolltilldelningen](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Konfigurera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-configure.md)
