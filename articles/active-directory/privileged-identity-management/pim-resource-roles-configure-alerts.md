---
title: Konfigurera säkerhetsaviseringar för Azure-resursroller i Privilegierad identitetshantering – Azure Active Directory | Microsoft-dokument
description: Lär dig hur du konfigurerar säkerhetsaviseringar för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023073"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurera säkerhetsaviseringar för Azure-resursroller i Privilegierad identitetshantering

Pim (Privileged Identity Management) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din Azure Active Directory -organisation (Azure Ad). När en avisering utlöses visas den på sidan Aviseringar.

![Azure-resurser – Aviseringar sida med avisering, risknivå och antal](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Granska aviseringar

Välj en avisering om du vill visa en rapport som visar de användare eller roller som utlöste aviseringen, tillsammans med reparationsvägledning.

![Varningsrapport som visar senaste genomsökningstid, beskrivning, begränsningssteg, typ, allvarlighetsgrad, säkerhetspåverkan och hur du förhindrar nästa gång](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar

| Varning | Severity | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare som tilldelats en resurs** |Medel |För många användare har ägarrollen. |Granska användarna i listan och tilldela om vissa till mindre privilegierade roller. |
| **För många permanenta ägare som tilldelats en resurs** |Medel |För många användare tilldelas permanent en roll. |Granska användarna i listan och tilldela en del om till att kräva aktivering för rollanvändning. |
| **Dubblettroll skapad** |Medel |Flera roller har samma villkor. |Använd bara en av dessa roller. |

### <a name="severity"></a>Severity

- **Hög**: Kräver omedelbar åtgärd på grund av en policyöverträdelse. 
- **Medium**: Kräver inte omedelbara åtgärder men signalerar en potentiell policyöverträdelse.
- **Låg**: Kräver inte omedelbara åtgärder men föreslår en föredragen policyändring.

## <a name="configure-security-alert-settings"></a>Konfigurera inställningar för säkerhetsvarningar

Gå till **Inställningar**på sidan Aviseringar .

![Sidan Aviseringar med inställningar markerade](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Anpassa inställningarna på de olika aviseringarna så att de fungerar med din miljö och dina säkerhetsmål.

![Ange sida för en avisering för att aktivera och konfigurera inställningar](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
