---
title: 'Översikt: Utför en åtkomst-granskning i Privileged Identity Management för Azure-resurser | Microsoft Docs'
description: Det här dokumentet beskriver hur du utför en åtkomst-granskning i PIM för Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751160"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Använd en resurs instrumentpanel för att utföra en åtkomst-granskning

Du kan använda instrumentpanelen för en resurs för att utföra en åtkomst-granskning i Privileged Identity Management (PIM) för Azure-resurser. Admin-visa instrumentpanelen innehåller tre huvudkomponenter:

- En grafisk representation av resursen rollen aktiveringar.
- Två diagram som visar fördelningen av rolltilldelningar med Tilldelningstyp.
- Ett dataområde som rör nya rolltilldelningar.

![Skärmbild för visning av-instrumentpanelen som visar diagram](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Skärmbild av visning av instrumentpanelen som visar data listor](media/azure-pim-resource-rbac/role-settings.png)

Grafisk representation av resursen rollen aktiveringar omfattar de senaste sju dagarna. Dessa data är begränsad till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, administratör för användaråtkomst) och för alla roller som kombineras.

Till höger om diagrammet aktiveringar visa diagrammen fördelningen av rolltilldelningar genom Tilldelningstyp för både användare och grupper. Du kan ändra värdet till ett procentvärde (eller vice versa) genom att välja en sektor i diagrammet.

Under diagrammen se antalet användare och grupper med nya rolltilldelningar över de senaste 30 dagarna och en lista över roller sorterade efter totala tilldelningar (i fallande ordning).


