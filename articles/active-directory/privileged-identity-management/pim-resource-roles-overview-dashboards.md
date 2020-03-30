---
title: Resursinstrumentpaneler för åtkomstgranskningar i PIM - Azure AD | Microsoft-dokument
description: Beskriver hur du använder en resursinstrumentpanel för att utföra en åtkomstgranskning i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847032"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Använda en resursinstrumentpanel för att utföra en åtkomstgranskning i Privilegierad identitetshantering

Du kan använda en resursinstrumentpanel för att utföra en åtkomstgranskning i PIM (Privileged Identity Management). Instrumentpanelen för administratörsvyn i Azure Active Directory (Azure AD) har tre primära komponenter:

- En grafisk representation av resursrollaktiveringar
- Diagram som visar fördelningen av rolltilldelningar efter tilldelningstyp
- Ett dataområde som innehåller information till nya rolltilldelningar

![Skärmbild av instrumentpanelen i administratörsvyn, som visar diagram och diagram](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Skärmbild av instrumentpanelen i administratörsvyn, som visar datalistor](media/pim-resource-roles-overview-dashboards/role-settings.png)

Den grafiska representationen av resursrollaktiveringar täcker de senaste sju dagarna. Dessa data begränsas till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, användaråtkomstadministratör) och för alla kombinerade roller.

På ena sidan av aktiveringsdiagrammet visar två diagram fördelningen av rolltilldelningar efter tilldelningstyp, för både användare och grupper. Du kan ändra värdet till en procentsats (eller vice versa) genom att välja en del av diagrammet.

Under diagrammen visas antalet användare och grupper med nya rolltilldelningar under de senaste 30 dagarna och roller sorterade efter totala tilldelningar i fallande ordning.

## <a name="next-steps"></a>Nästa steg

- [Starta en åtkomstgranskning för Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-start-access-review.md)
