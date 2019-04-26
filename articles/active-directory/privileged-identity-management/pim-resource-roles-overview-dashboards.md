---
title: Använd en resurs-instrumentpanel för att utföra en åtkomstgranskning i PIM - Azure Active Directory | Microsoft Docs
description: Beskriver hur du använder en resurs-instrumentpanel för att utföra en åtkomstgranskning i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37057e531ea8387fbed84c9b03bbfb9c14d160ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287582"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Använd en resurs-instrumentpanel för att utföra en åtkomstgranskning i PIM

Du kan använda en resurs-instrumentpanel för att utföra en åtkomstgranskning i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Admin-visa instrumentpanelen har tre huvudkomponenter:

- En grafisk representation av resursen rollaktiveringar.
- Två diagram som visar fördelningen av rolltilldelningar som Tilldelningstyp.
- Ett dataområde som hör till nya rolltilldelningar.

![Skärmbild av instrumentpanelen Admin-Visa diagram](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Skärmbild av Admin-visa instrumentpanelen som visar data som visas](media/azure-pim-resource-rbac/role-settings.png)

Grafisk representation av resursen rollaktiveringar omfattar de senaste sju dagarna. Dessa data är begränsad till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, administratör för användaråtkomst) och för alla roller som kombineras.

Till höger om diagrammet aktiveringar visa diagrammen fördelningen av rolltilldelningar genom Tilldelningstyp för både användare och grupper. Du kan ändra värdet till ett procentvärde (eller vice versa), genom att välja en sektor i diagrammet.

Under diagrammen visas antalet användare och grupper med nya rolltilldelningar över de senaste 30 dagarna och en lista över roller som sorterade efter Totalt antal tilldelningar (i fallande ordning).

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i PIM](pim-resource-roles-start-access-review.md) 
