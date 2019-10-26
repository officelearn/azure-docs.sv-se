---
title: Använd en resurs instrument panel för att utföra en åtkomst granskning i Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: de2c04a0ea109046848d443db2e6ab9a9dd21ee0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895578"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Använd en resurs instrument panel för att utföra en åtkomst granskning i Privileged Identity Management

Du kan använda en resurs instrument panel för att utföra en åtkomst granskning i Privileged Identity Management (PIM). Instrument panelen för administratörs visning i Azure Active Directory (Azure AD) har tre primära komponenter:

- En grafisk representation av resurs Rolls aktiveringar
- Diagram som visar distributionen av roll tilldelningar efter tilldelnings typ
- Ett data utrymme som innehåller information till nya roll tilldelningar

![Skärm bild av instrument panelen admin View, som visar diagram och diagram](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Skärm bild av instrument panelen admin View som visar data listor](media/pim-resource-roles-overview-dashboards/role-settings.png)

Den grafiska åter givningen av resurs Rolls aktiveringar omfattar de senaste sju dagarna. Dessa data är begränsade till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, användar åtkomst administratör) och för alla roller som kombineras.

På ena sidan om aktiverings diagrammet visar två diagram distributionen av roll tilldelningar efter tilldelnings typ, för både användare och grupper. Du kan ändra värdet till ett procenttal (eller vice versa) genom att välja en sektor i diagrammet.

Under diagrammen visas antalet användare och grupper med nya roll tilldelningar under de senaste 30 dagarna och roller sorterade efter totala tilldelningar i fallande ordning.

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomst granskning för Azures resurs roller i Privileged Identity Management](pim-resource-roles-start-access-review.md)
