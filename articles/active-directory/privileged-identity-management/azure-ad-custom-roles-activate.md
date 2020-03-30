---
title: Aktivera azure AD-anpassad roll – PRIVILEGIErad identitetshantering (PIM)
description: Aktivera en anpassad Azure AD-roll för pim (Privilegierad identitetshantering)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: cbd60d1311bd84adb303a0d329ab4e42f4d61525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498738"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktivera en anpassad Azure AD-roll i privilegierad identitetshantering

Privilegierad identitetshantering i Azure Active Directory (Azure AD) stöder nu just-in-time och tidsbundna tilldelningar till anpassade roller som skapats för programhantering i den administrativa upplevelsen för identitets- och åtkomsthantering. Mer information om hur du skapar anpassade roller för att delegera programhantering i Azure AD finns [i Anpassade administratörsroller i Azure Active Directory (förhandsversion).](../users-groups-roles/roles-custom-overview.md)

> [!NOTE]
> Azure AD-anpassade roller är inte integrerade med de inbyggda katalogrollerna under förhandsversionen. När kapaciteten är allmänt tillgänglig kommer rollhantering att ske i den inbyggda rollupplevelsen. Om du ser följande banner bör dessa roller hanteras [i den inbyggda rollupplevelsen](pim-how-to-activate-role.md) och den här artikeln gäller inte:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver aktivera en anpassad Azure AD-roll, begär aktivering genom att välja navigeringsalternativet Mina roller i Privilegierad identitetshantering.

1. Logga in [på Azure-portalen](https://portal.azure.com).
1. Öppna Azure [AD-privilegierad identitetshantering](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Välj **anpassade Azure AD-roller** för att se en lista över dina kvalificerade azure AD-anpassade rolltilldelningar.

   ![Se listan över kvalificerade anpassade Azure AD-rolltilldelningar](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Innan du tilldelar en roll måste du skapa/konfigurera en roll. För ytterligare information om hur du konfigurerar AAD Custom Roles se [här] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Hitta den tilldelning du behöver på sidan **Anpassade roller (förhandsversion)** i Azure AD.
1. Välj **Aktivera din roll** för att öppna sidan **Aktivera.**
1. Om din roll kräver multifaktorautentisering väljer du **Verifiera din identitet innan du fortsätter**. Du måste autentisera endast en gång per session.
1. Välj **Verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhetsverifiering.
1. Om du vill ange ett anpassat programomfång väljer du **Scope** för att öppna filterfönstret. Du bör begära åtkomst till en roll med minsta möjliga omfattning som behövs. Om din tilldelning sker i ett programomfång kan du bara aktivera i det omfånget.

   ![Tilldela ett Azure AD-resursomfattning till rolltilldelningen](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Om det behövs anger du en anpassad starttid för aktivering. När den används aktiveras rollmedlemmen vid den angivna tidpunkten.
1. Ange orsaken till aktiveringsbegäran i rutan **Orsak.** Dessa kan krävas eller inte i rollinställningen.
1. Välj **Aktivera**.

Om rollen inte kräver godkännande aktiveras den enligt dina inställningar och läggs till i listan över aktiva roller. Om du vill använda den aktiverade rollen börjar du med stegen i [Tilldela en azure AD-anpassad roll i Privilegierad identitetshantering](azure-ad-custom-roles-assign.md).

Om rollen kräver godkännande för att aktivera får du ett Azure-meddelande som informerar dig om att begäran väntar på godkännande.

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-configure.md)
- [Rolldefinitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
