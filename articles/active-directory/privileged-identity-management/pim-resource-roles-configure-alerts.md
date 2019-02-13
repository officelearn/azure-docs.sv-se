---
title: Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhetsaviseringar för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 538661df85ca0960992cb40a53b0e149c3b566b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189539"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM
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
* **Hög**: Kräver omedelbara åtgärder på grund av en Policyöverträdelse. 
* **Medel**: Kräver inte omedelbar åtgärd men signalerar potentiella Policyöverträdelse.
* **Låg**: Kräver inte omedelbar åtgärd men föreslår en önskad ändring.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsaviseringsinställningar
Från sidan aviseringar går du till **inställningar**.
![Inställningar](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningar för olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![Anpassa inställningar](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM](pim-resource-roles-configure-alerts.md)
