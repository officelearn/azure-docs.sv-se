---
title: Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver PIM säkerhetsaviseringar.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9a773d5ae8ba07dc73cd2587039e2029ce50152d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445009"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Hantera säkerhetsaviseringar för Azure-resurser med hjälp av Privileged Identity Management
Privileged Identity Management (PIM) för Azure-resurser genererar aviseringar när det finns misstänkt eller osäkra aktivitet i din miljö. När en avisering utlöses visas den på sidan aviseringar. 

![Sidan varningar](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Granska aviseringar
Välj en avisering om du vill se en rapport som visar den användare eller de roller som utlöste aviseringen, tillsammans med reparation råd.

![Aviseringsrapport](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar
| Varning | Severity | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare har tilldelats till en resurs** |Medel |För många användare har rollen ägare. |Granska användare i listan och omtilldela några mindre privilegierade roller. |
| **För många permanenta ägare har tilldelats till en resurs** |Medel |För många användare tilldelas permanent till en roll. |Granska användare i listan och tilldela några kräver aktivering för rollen. |
| **En duplicerad roll skapades** |Medel |Flera roller har samma villkor. |Använd bara en av dessa roller. |


### <a name="severity"></a>Severity
* **Hög**: kräver omedelbara åtgärder på grund av en Policyöverträdelse. 
* **Medel**: inte kräver omedelbar åtgärd men signalerar potentiella Policyöverträdelse.
* **Låg**: inte kräver omedelbar åtgärd men en ändring av en prioriterad.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsaviseringsinställningar
Från sidan aviseringar går du till **inställningar**.
![Inställningar](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningar för olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![Anpassa inställningar](media/azure-pim-resource-rbac/rbac-alert-settings.png)
