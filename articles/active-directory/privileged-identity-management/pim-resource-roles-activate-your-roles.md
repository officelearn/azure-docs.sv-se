---
title: Aktivera Mina roller för Azure-resurs i PIM | Microsoft Docs
description: Lär dig mer om att aktivera din Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189497"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivera Mina roller för Azure-resurs i PIM
Privileged Identity Management (PIM) introducerar en ny upplevelse i Aktivera roller för Azure-resurser. Berättigade rollmedlemmar kan schemalägga aktivering för ett framtida datum och tid. De kan också välja en specifik aktiveringsvaraktighet inom den Maxtid (konfigureras av administratörer). Mer information finns i [aktivera eller inaktivera roller i Azure AD Privileged Identity Management](pim-how-to-activate-role.md).

## <a name="activate-a-role"></a>Aktivera en roll
Bläddra till den **Mina roller** avsnitt i den vänstra rutan. Välj **aktivera** för den roll som du vill aktivera.

![Fliken ”berättigade roller” den ”Mina roller”-rutan.](media/azure-pim-resource-rbac/rbac-roles.png)

Från den **aktiveringar** menyn, ange startdatum och tid för att aktivera rollen. Du kan också minska aktiveringens varaktighet (hur lång tid som rollen är aktiv) och ange en motivering vid behov. Välj **aktivera**.

Om inte ändrat startdatum och starttid, aktiveras rollen på några sekunder. I den **Mina roller** fönstret Banderollmeddelandet visar att en roll är i kö för aktivering. Välj Uppdatera-knappen för att ta bort det här meddelandet.

![”Mina roller” fönstret med Banderollmeddelandet och ett meddelande om en väntar på godkännande](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Om aktiveringen är schemalagd för ett framtida datum och tid, väntande begäran visas på den **väntande begäranden om** fliken i det vänstra fönstret. Om rollaktivering krävs inte längre, kan du avbryta begäran genom att välja den **Avbryt** knappen.

![Lista över väntande begäranden med ”Avbryt”-knappar](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>Använd en roll omedelbart efter aktiveringen

På grund av cachelagring, görs inte aktiveringar direkt i Azure portal utan att en uppdatering. Om du vill minska risken för fördröjningar när du har aktiverat en roll kan du använda den **programåtkomst** i portalen. Program som nås från den här sidan söka efter nya rolltilldelningar omedelbart.

1. Öppna Azure AD Privileged Identity Management.

1. Klicka på den **programåtkomst** sidan.

    ![Programåtkomst med PIM - skärmbild](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Klicka på **Azure-resurser** att öppna portalen på den **alla resurser** sidan.

    När du klickar på den här länken du tvingar fram en uppdatering och det finns en kontroll för nya Azure-resurs-rolltilldelningar.

## <a name="apply-just-enough-administration-practices"></a>Tillämpa Just Enough Administration-metoder

Med din resurs rolltilldelningar tillräckligt Administration JEA ()-metodtips är enkelt med PIM för Azure-resurser. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![Att välja en resurs](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj **Mina roller** i den vänstra rutan och välj rätt roll för att aktivera. Tilldelningstypen är **ärvd** eftersom rollen har tilldelats på prenumerationen, i stället för vid resursgruppen.

![Lista över berättigade rolltilldelningar med Tilldelningstyp markerat](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera mitt Azure AD-katalogroller i PIM](pim-how-to-activate-role.md)