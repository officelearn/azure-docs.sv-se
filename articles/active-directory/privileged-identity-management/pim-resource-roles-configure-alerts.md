---
title: Konfigurera säkerhets aviseringar för Azures resurs roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhets aviseringar för Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804230"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurera säkerhets aviseringar för Azures resurs roller i PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din miljö. När en avisering utlöses visas den på sidan aviseringar. 

![Azure-resurser – aviseringar Visa avisering, risk nivå och antal](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Granska aviseringar
Välj en avisering om du vill visa en rapport som visar de användare eller roller som utlöste aviseringen, tillsammans med reparations råd.

![Varnings rapport om senaste genomsöknings tid, beskrivning, minsknings steg, typ, allvarlighets grad, säkerhets påverkan och hur du förhindrar nästa gång](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar
| Varning | severity | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare har tilldelats till en resurs** |Medel |För många användare har ägar rollen. |Granska användarna i listan och tilldela om några till mindre privilegierade roller. |
| **För många permanenta ägare har tilldelats till en resurs** |Medel |För många användare tilldelas permanent till en roll. |Granska användarna i listan och tilldela om några för att kräva aktivering för roll användning. |
| **En duplicerad roll har skapats** |Medel |Flera roller har samma villkor. |Använd endast en av dessa roller. |


### <a name="severity"></a>severity
* **Hög**: Kräver omedelbar åtgärd på grund av en princip överträdelse. 
* **Medel**: Kräver ingen omedelbar åtgärd, men signalerar en möjlig princip överträdelse.
* **Låg**: Kräver ingen omedelbar åtgärd, men föreslår en önskad princip ändring.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhets aviserings inställningar
Gå till **Inställningar**på sidan aviseringar.

![Sidan aviseringar med markerade inställningar](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Anpassa inställningarna för de olika aviseringarna så att de fungerar med dina miljö-och säkerhets mål.

![Inställnings sida för en avisering för att aktivera och konfigurera inställningar](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
