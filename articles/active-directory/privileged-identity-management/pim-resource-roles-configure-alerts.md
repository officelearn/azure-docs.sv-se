---
title: Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver PIM säkerhetsaviseringar.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c6c057541b3e3067de6331bab6ca9cccfa092710
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management
Privileged Identity Management (PIM) för Azure-resurser genererar aviseringar när det är misstänkt eller unsafe aktivitet i din miljö. När en avisering utlöses visas den på sidan aviseringar. 

![Sidan varningar](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Granska aviseringar
Välj en avisering om du vill se en rapport som listar användare eller roller som utlöste aviseringen, tillsammans med reparation råd.

![Aviseringsrapport](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar
| Varning | Allvarsgrad | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare som har tilldelats en resurs** |Medel |För många användare har rollen som ägare. |Granska användare i listan och omtilldela några mindre privilegierade roller. |
| **För många permanent ägare som har tilldelats en resurs** |Medel |För många användare är permanent tilldelade till en roll. |Granska användare i listan och tilldela några kräver aktivering för rollen genom att använda. |
| **Duplicera roll som skapas** |Medel |Flera roller har samma villkor. |Använd bara en av rollerna. |


### <a name="severity"></a>Allvarsgrad
* **Hög**: kräver omedelbara åtgärder på grund av en principöverträdelse. 
* **Medel**: inte kräver omedelbara åtgärder men signalerar potentiella principöverträdelsen.
* **Låg**: inte kräver omedelbara åtgärder men föreslår önskad princip.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsinställningar för avisering
Gå till från sidan aviseringar **inställningar**.
![Inställningar](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningarna på olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![Anpassa inställningar](media/azure-pim-resource-rbac/rbac-alert-settings.png)
