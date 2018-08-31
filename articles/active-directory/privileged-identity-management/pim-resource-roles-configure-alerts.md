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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 33bd13421b0c1574475516da942abe4e8c71ed52
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188840"
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
* **Hög**: kräver omedelbara åtgärder på grund av en Policyöverträdelse. 
* **Medel**: inte kräver omedelbar åtgärd men signalerar potentiella Policyöverträdelse.
* **Låg**: inte kräver omedelbar åtgärd men en ändring av en prioriterad.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsaviseringsinställningar
Från sidan aviseringar går du till **inställningar**.
![Inställningar](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Anpassa inställningar för olika aviseringar för att arbeta med din miljö och säkerhetsmål.
![Anpassa inställningar](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Kräv multifaktorautentisering för Azure-resursroller i PIM](pim-resource-roles-require-mfa.md)
- [Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM](pim-resource-roles-configure-alerts.md)
