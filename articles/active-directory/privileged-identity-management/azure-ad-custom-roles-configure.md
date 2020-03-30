---
title: Konfigurera anpassad Azure AD-roll – PIM (Privileged Identity Management)
description: Konfigurera Azure AD-anpassade roller i PIM (Privileged Identity Management)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498683"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurera anpassade Azure AD-roller i privilegierad identitetshantering

En privilegierad rolladministratör kan ändra de rollinställningar som gäller för en användare när de aktiverar tilldelningen till en anpassad roll och för andra programadministratörer som tilldelar anpassade roller.

> [!NOTE]
> Azure AD-anpassade roller är inte integrerade med de inbyggda katalogrollerna under förhandsversionen. När kapaciteten är allmänt tillgänglig kommer rollhantering att ske i den inbyggda rollupplevelsen. Om du ser följande banner bör dessa roller hanteras [i den inbyggda rollupplevelsen](pim-how-to-activate-role.md) och den här artikeln gäller inte:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ dessa steg för att öppna inställningarna för en Azure AD-roll.

1. Logga in [på privilegierad identitetshantering](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure-portalen med ett användarkonto som har tilldelats rollen Privilegierad rolladministratör.
1. Välj **anpassade Azure AD-roller (förhandsversion)**.

    ![Välj förhandsversion av anpassade roller för anpassade roller i Azure AD för att se kvalificerade rolltilldelningar](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Välj **Inställning** för att öppna sidan **Inställningar.** Välj roll för de inställningar som du vill konfigurera.
1. Välj **Redigera** om du vill öppna sidan **Rollinställningar.**

    ![Öppna den anpassade azure AD-rollen för att redigera inställningar](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Rollinställningar

Det finns flera inställningar som du kan konfigurera.

### <a name="assignment-duration"></a>Tilldelningens varaktighet

Du kan välja mellan två tilldelningslängdsalternativ för varje tilldelningstyp (kvalificerad eller aktiv) när du konfigurerar inställningar för en roll. Dessa alternativ blir standardtidslängd när en medlem tilldelas rollen i Privilegierad identitetshantering.

Du kan välja ett av dessa *berättigade* tilldelningslängdsalternativ.

- **Tillåt permanent berättigad tilldelning:** Administratörer kan tilldela permanent kvalificerat medlemskap.
- **Förfalla berättigad tilldelning efter**: Administratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start- och slutdatum.

Du kan också välja ett av dessa *aktiva* tilldelningsvaraktighetsalternativ:

- **Tillåt permanent aktiv tilldelning:** Administratörer kan tilldela permanent aktivt medlemskap.
- **Upphör att gälla aktiv tilldelning efter**: Administratörer kan kräva att alla aktiva tilldelningar har ett angivet start- och slutdatum.

### <a name="require-azure-multi-factor-authentication"></a>Kräv Azure Multi-Factor Authentication

Privilegierad identitetshantering ger valfri tillämpning av Azure Multi-Factor Authentication för två olika scenarier.

- **Kräv multifaktorautentisering vid aktiv tilldelning**

  Om du bara vill tilldela en medlem till en roll under en kort tid (till exempel en dag) kan det vara för långsamt att kräva att de tilldelade medlemmarna begär aktivering. I det här fallet kan privilegierad identitetshantering inte framtvinga multifaktorautentisering när användaren aktiverar sin rolltilldelning, eftersom de redan är aktiva i rollen från det ögonblick de tilldelas. Om du vill vara så att administratören som fullgör tilldelningen är den de säger att de är väljer du rutan **Kräv multifaktorautentisering på aktiv tilldelning.**

- **Kräv multifaktorautentisering vid aktivering**

  Du kan kräva att berättigade användare som tilldelats en roll för att registrera sig i Azure Multi-Factor Authentication innan de kan aktiveras. Denna process säkerställer att användaren som begär aktivering är vem de säger att de är med rimlig säkerhet. Om du tillämpar det här alternativet skyddas kritiska roller i situationer där användarkontot kan ha komprometterats. Om du vill att en kvalificerad medlem ska köra Azure Multi-Factor Authentication före aktivering väljer du kryssrutan **Kräv multifaktorautentisering vid aktivering.**

Mer information finns i [Multifaktorautentisering och Privilegierad identitetshantering](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maximal varaktighet för aktivering

Använd skjutreglaget För maximal varaktighet för **aktivering** för att ange den maximala tid, i timmar, som en roll förblir aktiv innan den upphör att gälla. Det här värdet kan vara från, 1 och 24 timmar.

### <a name="require-justification"></a>Kräv motivering

Du kan kräva att medlemmar anger en motivering för aktiv tilldelning eller när de aktiveras. Om du vill kräva justering markerar du kryssrutan **Kräv justering vid aktiv tilldelning** eller kryssrutan **Kräv justering vid aktivering.**

### <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Om du vill kräva godkännande för att aktivera en roll följer du dessa steg.

1. Markera kryssrutan **Kräv godkännande för att aktivera.**
1. Välj **Välj godkännare** om du vill öppna listan **Välj en medlem eller grupp.**

    ![Öppna den anpassade azure AD-rollen för att redigera inställningar](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Markera minst en medlem eller grupp och klicka sedan på **Markera**. Du måste välja minst en godkännare. Det finns inga standardgodkännare. Dina val visas i listan över valda godkännare.
1. När du har angett rollinställningarna väljer du **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-activate.md)
- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-update-remove.md)
- [Rolldefinitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
