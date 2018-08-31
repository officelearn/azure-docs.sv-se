---
title: Använda en resurs-instrumentpanel för att utföra en åtkomstgranskning – Azure | Microsoft Docs
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
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 20172cf7413397aedc4b3c32d0f1419531a2588a
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188505"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Använda en resurs-instrumentpanel för att utföra en åtkomstgranskning

Du kan använda en resurs-instrumentpanel för att utföra en åtkomstgranskning i Privileged Identity Management (PIM) för Azure-resurser. Admin-visa instrumentpanelen har tre huvudkomponenter:

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
