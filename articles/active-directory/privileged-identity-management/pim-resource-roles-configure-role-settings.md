---
title: Konfigurera inställningar för Azure-resurs roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure-resurs roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88645d07ff213b0b7a56f2b081f0aaed01fd2929
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804198"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurera inställningar för Azure-resurs roller i PIM

När du konfigurerar inställningar för Azure-resurs roller definierar du de standardinställningar som tillämpas på Azure-resursens roll tilldelningar i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Använd följande procedurer för att konfigurera arbets flödet för godkännande och ange vem som kan godkänna eller neka begär Anden.

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ dessa steg för att öppna inställningarna för en Azure-resurs roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill hantera, till exempel en prenumeration eller hanterings grupp.

    ![Sidan Azure-resurser visar resurser som kan hanteras](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Klicka på **roll inställningar**.

    ![Sidan roll inställningar visar Azure-resurs roller](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Klicka på den roll vars inställningar du vill konfigurera.

    ![Sidan information om roll inställning visar flera tilldelnings-och aktiverings inställningar](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Klicka på **Redigera** för att öppna fönstret roll inställningar.

    ![Sidan Redigera roll inställningar med alternativ för att uppdatera tilldelnings-och aktiverings inställningar](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    I fönstret roll inställning för varje roll finns det flera inställningar som du kan konfigurera.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Du kan välja mellan två alternativ för tilldelnings varaktighet för varje tilldelnings typ (giltig och aktiv) när du konfigurerar inställningar för en roll. De här alternativen blir den maximala standard varaktigheten när en medlem tilldelas rollen i PIM.

Du kan välja något av följande **tillgängliga** varaktighets alternativ för tilldelning:

| | |
| --- | --- |
| **Tillåt permanent berättigad tilldelning** | Resurs administratörer kan tilldela permanent kvalificerat medlemskap. |
| **Giltig tilldelning har förfallit efter** | Resurs administratörer kan kräva att alla kvalificerade tilldelningar har ett angivet start-och slutdatum. |

Och du kan välja något av dessa alternativ för varaktighet för **aktiv** tilldelning:

| | |
| --- | --- |
| **Tillåt permanent aktiv tilldelning** | Resurs administratörer kan tilldela permanenta aktiva medlemskap. |
| **Aktiv tilldelning förfaller efter** | Resurs administratörer kan kräva att alla aktiva tilldelningar har ett angivet start-och slutdatum. |

> [!NOTE] 
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resurs administratörer. Medlemmar kan också initiera självbetjänings begär Anden för att [utöka eller förnya roll tilldelningar](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

PIM tillhandahåller valfri tillämpning av Azure Multi-Factor Authentication (MFA) för två olika scenarier.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Kräv Multi-Factor Authentication för aktiv tilldelning

I vissa fall kanske du vill tilldela en medlem till en roll för en kort varaktighet (till exempel en dag). I det här fallet behöver de inte de tilldelade medlemmarna för att begära aktivering. I det här scenariot kan inte PIM tillämpa MFA när medlemmen använder sin roll tilldelning, eftersom de redan är aktiva i rollen från det ögonblick de tilldelats.

För att säkerställa att resurs administratören som uppfyller tilldelningen är den som de säger att de är, kan du tillämpa MFA på aktiv tilldelning genom att markera rutan **kräv Multi-Factor Authentication i den aktiva tilldelningen** .

### <a name="require-multi-factor-authentication-on-activation"></a>Kräv Multi-Factor Authentication vid aktivering

Du kan behöva berättigade medlemmar i en roll för att köra MFA innan de kan aktivera. Den här processen säkerställer att användaren som begär aktiveringen är vem de säger att de är i rimlig säkerhet. Att framtvinga det här alternativet skyddar viktiga resurser i situationer när användar kontot kan ha komprometterats.

Om du vill kräva att en berättigad medlem kör MFA innan du aktiverar den, markerar du kryss rutan **kräv Multi-Factor Authentication vid aktivering** .

Mer information finns i [Multi-Factor Authentication (MFA) och PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maxlängd för aktivering

Använd skjutreglaget **maximal varaktighet** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara mellan 1 och 24 timmar.

## <a name="require-justification"></a>Kräv motivering

Du kan kräva att medlemmar anger en motivering för aktiv tilldelning eller när de aktiverar. Om du vill kräva en motivering markerar du kryss rutan **Kräv motivering för aktiva tilldelningar** eller kryss rutan **Kräv motivering för aktivering** .

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Markera kryss rutan **Kräv godkännande för aktivering** .

1. Klicka på **Välj god kännare** för att öppna fönstret Välj en medlem eller grupp.

    ![Välj en medlem eller ett grupp fönster för att välja god kännare](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Välj minst en medlem eller grupp och klicka sedan på **Välj**. Du kan lägga till valfri kombination av medlemmar och grupper. Du måste välja minst en god kännare. Det finns inga standard god kännare.

    Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar klickar du på **Uppdatera** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resurs roller i PIM](pim-resource-roles-assign-roles.md)
- [Konfigurera säkerhets aviseringar för Azures resurs roller i PIM](pim-resource-roles-configure-alerts.md)
