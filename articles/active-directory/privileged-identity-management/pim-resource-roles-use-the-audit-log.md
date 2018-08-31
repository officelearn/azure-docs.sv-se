---
title: Visa granskningshistorik för Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du visar granskningshistorik för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189319"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>Visa granskningshistorik för Azure-resursroller i PIM

Resursgranskning ger dig en överblick över alla roll-aktivitet för resursen. Du kan filtrera informationen med ett fördefinierat datum eller ett anpassat intervall.
![Filtrera information](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Resursgranskning tillhandahåller även snabb åtkomst till aktivitetsinformation för en användare. Under **granskningstyp**väljer **aktivera**. Välj **(aktivitet)** att se användarens åtgärder i Azure-resurser.
![Aktivitetsinformation](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Mer information om klientaktivitet](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>Min granskning

Min granskning ger en översikt över en användares personliga rollen aktivitet. Du kan filtrera informationen med ett fördefinierat datum eller ett anpassat intervall.
![Personliga rollen aktivitet](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och Azure-resurs-aktivitet

Om du vill se vilka åtgärder som en viss användare har gjort i olika resurser, kan du granska aktiviteten Azure-resurs som är associerat med en viss aktiveringsperioden. Starta genom att välja en användare från den **medlemmar** vy eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk vy av användarens åtgärder i Azure-resurser efter datum. Den visar även de senaste rollaktiveringar under den samma tidsperioden.

![Användarinformationen](media/azure-pim-resource-rbac/rbac-user-details.png)

Att välja en specifik rollaktivering visar rollaktiveringsinformation och motsvarande Azure-resurs-aktivitet som inträffade när användaren var aktiv.

![Välj rollaktivering](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>Nästa steg

- [Visa granskningshistorik för Azure AD-katalogroller i PIM](pim-how-to-use-audit-log.md)
