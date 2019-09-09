---
title: Använd en resurs instrument panel för att utföra en åtkomst granskning i PIM – Azure Active Directory | Microsoft Docs
description: Beskriver hur du använder en resurs instrument panel för att utföra en åtkomst granskning i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804112"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Använda en resurs instrument panel för att utföra en åtkomst granskning i PIM

Du kan använda en resurs instrument panel för att utföra en åtkomst granskning i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Instrument panelen för administratörs vy har tre primära komponenter:

- En grafisk representation av resurs Rolls aktiveringar.
- Två diagram som visar distributionen av roll tilldelningar efter tilldelnings typ.
- Ett data utrymme som rör nya roll tilldelningar.

![Skärm bild av instrument panelen admin View, som visar diagram och diagram](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Skärm bild av instrument panelen admin View som visar data listor](media/pim-resource-roles-overview-dashboards/role-settings.png)

Den grafiska åter givningen av resurs Rolls aktiveringar omfattar de senaste sju dagarna. Dessa data är begränsade till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, användar åtkomst administratör) och för alla roller som kombineras.

Till höger om aktiverings diagrammet visar två diagram distributionen av roll tilldelningar efter tilldelnings typ, för både användare och grupper. Du kan ändra värdet till ett procenttal (eller vice versa) genom att välja en sektor i diagrammet.

Under diagrammen ser du antalet användare och grupper med nya roll tilldelningar under de senaste 30 dagarna och en lista över roller sorterade efter de totala tilldelningarna (i fallande ordning).

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i PIM](pim-resource-roles-start-access-review.md) 
