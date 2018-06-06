---
title: Granska resursroller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du kan få en överblick över alla aktiviteter i rollen för den angivna resursen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 714c8ba2a6acf1fdb73fe53f9d2aaf1d98ee9abc
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699758"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Granska resursroller för Azure-resurser med hjälp av Privileged Identity Management 

Granskning för resursen ger dig en överblick över alla aktiviteter i rollen för resursen. Du kan filtrera informationen genom att använda ett fördefinierat datum eller ett anpassat intervall.
![Filtrera information](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Resursen audit också ger snabb åtkomst till en användarens aktivitet information. Under **granska typen**väljer **aktivera**. Välj **(aktivitet)** att se användarens åtgärder i Azure-resurser.
![Information om aktiviteten](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Mer information för aktiviteten](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Min granskning

Min granskning ger en översikt över aktivitet för en användares personliga roll. Du kan filtrera informationen genom att använda ett fördefinierat datum eller ett anpassat intervall.
![Personliga rollen aktivitet](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och Azure-resurshanteraren aktivitet

Du kan granska aktiviteten Azure-resurs som är associerad med en viss aktiveringsperioden om du vill se vilka åtgärder som en viss användare tog i olika resurser. Starta genom att välja en användare från den **medlemmar** vy eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk vy av användarens åtgärder i Azure-resurser efter datum. Den visar även senaste rollen aktiveringar via samma tidsperioden.

![Användarinformationen](media/azure-pim-resource-rbac/rbac-user-details.png)

Att välja en specifik rollaktivering visar rollinformation för aktivering och motsvarande Azure-resurs-aktivitet som inträffade medan användaren var aktiv.

![Välj rollaktivering](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

