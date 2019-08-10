---
title: Aktivera en anpassad Azure AD-roll i Privileged Identity Management (PIM) | Microsoft Docs
description: Så här aktiverar du en anpassad Azure AD-roll för tilldelning Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e38b0f4463c2188f0b2da6ebb1b57d08af69b41f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947333"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktivera en anpassad Azure AD-roll i Privileged Identity Management

Privileged Identity Management i Azure Active Directory (Azure AD) har nu stöd för just-in-Time-och Time-Bound-tilldelning till anpassade roller som skapats för program hantering i den administrativa upplevelsen för identitets-och åtkomst hantering. Mer information om hur du skapar anpassade roller för att delegera program hantering i Azure AD finns i [anpassade administratörs roller i Azure Active Directory (för hands version)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Anpassade Azure AD-roller är inte integrerade med de inbyggda katalog rollerna under för hands versionen. När funktionen är allmänt tillgänglig, sker roll hanteringen i den inbyggda roll upplevelsen.

## <a name="activate-a-role"></a>Aktivera en roll

När du behöver aktivera en anpassad Azure AD-roll kan du begära aktivering genom att välja navigerings alternativet mina roller i PIM.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna Azure AD- [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Välj **Azure AD-anpassade roller** för att se en lista över dina berättigade Azure AD-anpassade roll tilldelningar.

   ![Se listan över berättigade Azure AD-anpassade roll tilldelningar](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

1. På sidan **anpassade Azure AD-roller (för hands version)** hittar du den tilldelning du behöver.
1. Välj **aktivera din roll** för att öppna sidan **Aktivera** .
1. Om din roll kräver Multi-Factor Authentication väljer du **Verifiera din identitet innan du fortsätter**. Du måste bara autentisera en gång per session.
1. Välj **verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhets verifiering.
1. Om du vill ange en anpassad program omfattning väljer du **omfång** för att öppna filter fönstret. Du bör begära åtkomst till en roll med minimal omfattning som behövs. Om tilldelningen är i ett program område kan du bara aktivera i det omfånget.

   ![Tilldela ett Azure AD-resurs omfång till roll tilldelningen](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Vid behov kan du ange en start tid för anpassad aktivering. När den används aktive ras roll medlemmen vid den angivna tiden.
1. Ange orsaken till aktiverings förfrågan i rutan **orsak** . De kan göras nödvändiga eller inte i roll inställningen.
1. Välj **Aktivera**.

Om rollen inte kräver godkännande aktive ras den enligt dina inställningar och läggs till i listan över aktiva roller. Om du vill använda den aktiverade rollen börjar du med stegen i [tilldela en anpassad Azure AD-roll i Privileged Identity Management](azure-ad-custom-roles-assign.md).

Om rollen kräver godkännande för att aktivera får du ett Azure-meddelande som informerar dig om att begäran väntar på godkännande.

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-configure.md)
- [Roll definitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
