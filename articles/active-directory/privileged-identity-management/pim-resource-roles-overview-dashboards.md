---
title: Hur du utför en åtkomst-granskning i Privileged Identity Management för Azure-resurser | Microsoft Docs
description: Det här dokumentet beskriver hur du utför och komma åt granska i PIM för Azure-resurser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>Resursen instrumentpaneler

Visning av instrumentpanelen har fyra huvudsakliga komponenter. En grafisk representation av resursen rollen aktiveringar under de senaste sju dagarna. Dessa data är begränsad till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, administratör för användaråtkomst) och alla roller som kombineras.

Till höger om diagrammet aktiveringar finns två diagram som visar fördelningen av rolltilldelningar med Tilldelningstyp för både användare och grupper. Om du väljer en sektor i diagrammet ändras värdet till ett procentvärde (eller vice versa).

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

Under diagrammen se antalet användare och grupper med nya rolltilldelningar över de senaste 30 dagarna (vänster) och en lista över roller sorterade efter totala tilldelningar (fallande).

![](media/azure-pim-resource-rbac/role-settings.png)
