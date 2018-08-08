---
title: Granska resursroller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du få en överblick över alla aktiviteter i rollen för den en viss resurs.
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
ms.openlocfilehash: 8f328a609d696886ed452589d3cdfb5f45aec62a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621291"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Granska resursroller för Azure-resurser med hjälp av Privileged Identity Management 

Resursgranskning ger dig en överblick över alla roll-aktivitet för resursen. Du kan filtrera informationen med ett fördefinierat datum eller ett anpassat intervall.
![Filtrera information](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Resursgranskning tillhandahåller även snabb åtkomst till aktivitetsinformation för en användare. Under **granskningstyp**väljer **aktivera**. Välj **(aktivitet)** att se användarens åtgärder i Azure-resurser.
![Aktivitetsinformation](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Mer information om klientaktivitet](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Min granskning

Min granskning ger en översikt över en användares personliga rollen aktivitet. Du kan filtrera informationen med ett fördefinierat datum eller ett anpassat intervall.
![Personliga rollen aktivitet](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och aktivitet för Azure-resurs

Om du vill se vilka åtgärder som en viss användare har gjort i olika resurser, kan du granska aktiviteten Azure-resurs som är associerat med en viss aktiveringsperioden. Starta genom att välja en användare från den **medlemmar** vy eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk vy av användarens åtgärder i Azure-resurser efter datum. Den visar även de senaste rollaktiveringar under den samma tidsperioden.

![Användarinformationen](media/azure-pim-resource-rbac/rbac-user-details.png)

Att välja en specifik rollaktivering visar rollaktiveringsinformation och motsvarande Azure-resurs-aktivitet som inträffade när användaren var aktiv.

![Välj rollaktivering](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

