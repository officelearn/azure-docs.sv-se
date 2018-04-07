---
title: Privileged Identity Management resurser för Azure - säkerhetsaviseringar | Microsoft Docs
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
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - resursroller - aviseringar
PIM resurser för Azure genererar aviseringar när det är misstänkt eller unsafe aktivitet i din miljö. När en avisering utlöses visas den på sidan aviseringar. 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Granska aviseringar
Välj en avisering om du vill se en rapport som listar användare eller roller som utlöste aviseringen samt reparation råd.
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar
| Varning | Allvarsgrad | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare som har tilldelats en resurs** |Medel |För många användare har rollen som ägare. |Granska användare i listan och tilldela några mindre privilegierade roller. |
| **För många permanent ägare som har tilldelats en resurs** |Medel |För många användare är permanent tilldelade till en roll. |Granska användare i listan och tilldela några kräver aktivering för rollen genom att använda. |
| **Duplicera roll som skapas** |Medel |Flera roller har samma villkor. |Använd bara en av rollerna. |


### <a name="severity"></a>Allvarsgrad
* **Hög**: kräver omedelbara åtgärder på grund av en principöverträdelse. 
* **Medel**: inte kräver omedelbara åtgärder men signalerar potentiella principöverträdelsen.
* **Låg**: inte kräver omedelbara åtgärder men föreslår preferrable princip.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsinställningar för avisering
Gå till inställningar på sidan aviseringar.
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningarna på olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
