---
title: Konfigurera Azure AD Custom Role-Privileged Identity Management (PIM)
description: Så här konfigurerar du anpassade Azure AD-roller i Privileged Identity Management (PIM)
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
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77498683"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurera anpassade Azure AD-roller i Privileged Identity Management

En privilegie rad roll administratör kan ändra de roll inställningar som gäller för en användare när de aktiverar sin tilldelning till en anpassad roll och för andra program administratörer som tilldelar anpassade roller.

> [!NOTE]
> Anpassade Azure AD-roller är inte integrerade med de inbyggda katalog rollerna under för hands versionen. När funktionen är allmänt tillgänglig, sker roll hanteringen i den inbyggda roll upplevelsen. Om du ser följande banderoll bör dessa roller hanteras [i den inbyggda roll upplevelsen](pim-how-to-activate-role.md) och den här artikeln gäller inte:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ de här stegen för att öppna inställningarna för en Azure AD-roll.

1. Logga in på [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure Portal med ett användar konto som tilldelats rollen privilegie rad roll administratör.
1. Välj **anpassade Azure AD-roller (för hands version)**.

    ![Välj för hands versionen av Azure AD-anpassade roller för att se kvalificerade roll tilldelningar](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Välj **inställning** för att öppna sidan **Inställningar** . Välj rollen för de inställningar som du vill konfigurera.
1. Välj **Redigera** för att öppna sidan **roll inställningar** .

    ![Öppna den anpassade Azure AD-rollen för att redigera inställningar](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Roll inställningar

Det finns flera inställningar som du kan konfigurera.

### <a name="assignment-duration"></a>Varaktighet för tilldelning

Du kan välja mellan två alternativ för tilldelnings varaktighet för varje tilldelnings typ (giltig eller aktiv) när du konfigurerar inställningar för en roll. De här alternativen blir den maximala standard varaktigheten när en medlem tilldelas rollen i Privileged Identity Management.

Du kan välja ett av de här *giltiga* alternativen för tilldelnings varaktighet.

- **Tillåt permanent berättigad tilldelning**: administratörer kan tilldela permanent kvalificerat medlemskap.
- **Giltig tilldelning efter**: administratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start-och slutdatum.

Du kan också välja något av dessa alternativ för varaktighet för *aktiv* tilldelning:

- **Tillåt permanent aktiv tilldelning**: administratörer kan tilldela permanenta aktiva medlemskap.
- Den **aktiva tilldelningen upphör att gälla efter**: administratörer kan kräva att alla aktiva tilldelningar har ett angivet start-och slutdatum.

### <a name="require-azure-multi-factor-authentication"></a>Kräv Azure Multi-Factor Authentication

Privileged Identity Management tillhandahåller en valfri tillämpning av Azure Multi-Factor Authentication för två olika scenarier.

- **Kräv Multi-Factor Authentication för aktiv tilldelning**

  Om du bara vill tilldela en medlem en roll för en kort varaktighet (en dag, till exempel) kan det vara för långsamt att kräva att de tilldelade medlemmarna begär aktivering. I det här scenariot kan Privileged Identity Management inte tillämpa Multi-Factor Authentication när användaren aktiverar sin roll tilldelning, eftersom de redan är aktiva i rollen från det ögonblick de tilldelats. För att säkerställa att administratören som uppfyller tilldelningen är den som de säger att de är, markerar du rutan **kräv Multi-Factor Authentication i den aktiva tilldelningen** .

- **Kräv Multi-Factor Authentication vid aktivering**

  Du kan kräva att berättigade användare har tilldelats en roll för att registreras i Azure Multi-Factor Authentication innan de kan aktive ras. Den här processen säkerställer att användaren som begär aktiveringen är vem de säger att de är i rimlig säkerhet. Att framtvinga det här alternativet skyddar viktiga roller i situationer när användar kontot kan ha komprometterats. Om du vill att en berättigad medlem ska kunna köra Azure Multi-Factor Authentication före aktiveringen väljer du kryss rutan **kräv Multi-Factor Authentication vid aktivering** .

Mer information finns i [Multi-Factor Authentication och Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget **maximal varaktighet** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara från, 1 och 24 timmar.

### <a name="require-justification"></a>Kräv motivering

Du kan kräva att medlemmar anger en motivering för aktiv tilldelning eller när de aktiverar. Om du vill kräva en motivering markerar du kryss rutan **Kräv motivering för aktiv tilldelning** eller kryss rutan **Kräv motivering för aktivering** .

### <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Markera kryss rutan **Kräv godkännande för aktivering** .
1. Välj **Välj god kännare** för att öppna listan **Välj en medlem eller grupp** .

    ![Öppna den anpassade Azure AD-rollen för att redigera inställningar](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Välj minst en medlem eller grupp och klicka sedan på **Välj**. Du måste välja minst en god kännare. Det finns inga standard god kännare. Dina val kommer att visas i listan över valda god kännare.
1. När du har angett roll inställningarna väljer du **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-activate.md)
- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-update-remove.md)
- [Roll definitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
