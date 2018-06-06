---
title: Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver PIM säkerhetsaviseringar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0ad4cde6a59a8867f9ff3b3cb9d9bded06e34256
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699591"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management
Privileged Identity Management (PIM) för Azure-resurser genererar aviseringar när det är misstänkt eller unsafe aktivitet i din miljö. När en avisering utlöses visas den på sidan aviseringar. 

![Sidan varningar](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Granska aviseringar
Välj en avisering om du vill se en rapport som listar användare eller roller som utlöste aviseringen, tillsammans med reparation råd.

![Aviseringsrapport](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar
| Varning | Severity | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare som har tilldelats en resurs** |Medel |För många användare har rollen som ägare. |Granska användare i listan och omtilldela några mindre privilegierade roller. |
| **För många permanent ägare som har tilldelats en resurs** |Medel |För många användare är permanent tilldelade till en roll. |Granska användare i listan och tilldela några kräver aktivering för rollen genom att använda. |
| **Duplicera roll som skapas** |Medel |Flera roller har samma villkor. |Använd bara en av rollerna. |


### <a name="severity"></a>Severity
* **Hög**: kräver omedelbara åtgärder på grund av en principöverträdelse. 
* **Medel**: inte kräver omedelbara åtgärder men signalerar potentiella principöverträdelsen.
* **Låg**: inte kräver omedelbara åtgärder men föreslår önskad princip.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsinställningar för avisering
Gå till från sidan aviseringar **inställningar**.
![Inställningar](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningarna på olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![Anpassa inställningar](media/azure-pim-resource-rbac/rbac-alert-settings.png)
