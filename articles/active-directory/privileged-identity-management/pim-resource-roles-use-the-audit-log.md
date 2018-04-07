---
title: Privileged Identity Management resurser för Azure - resurs audit | Microsoft Docs
description: Beskriver hur du kan få en överblick över alla aktiviteter i rollen för den angivna resursen.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - resursroller - granskning

Granskning för resursen ger dig en överblick över alla aktiviteter i rollen för resursen. Du kan filtrera informationen genom att använda ett fördefinierat datum eller ett anpassat intervall.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Resursen audit också ger snabb åtkomst om du vill visa information om en användare-aktivitet. Välj ”Aktivera” under ”Audit typ”. Klicka på ”(aktivitet)” för att se användarens åtgärder på Azure-resurser.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Min granskning

Min granskning ger en översikt över aktivitet för en användares personliga roll. Du kan filtrera informationen genom att använda ett fördefinierat datum eller ett anpassat intervall.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och Azure-resurshanteraren aktivitet

Om du behöver se vilka åtgärder som en viss användare tog på olika resurser kan du granska Azure-resurshanteraren aktiviteten som associeras med en viss aktiveringsperioden (för behöriga användare). Starta genom att välja en användare från vyn medlemmar eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk vy av användarens åtgärder på Azure-resurser efter datum och de senaste rollen aktiveringarna via samma tidsperioden.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Att välja en specifik rollaktivering visas roll aktiveringsinformation och motsvarande Azure-resurshanteraren aktivitet som inträffade medan användaren var aktiv.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

