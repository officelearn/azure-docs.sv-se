---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b4b06119b9d46781b967fc8d98808c60d2b41ccb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753694"
---
Du vill kontrollera att användarna i din organisation har rätt åtkomstnivå till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också försäkra dig om att de kan utföra sitt arbete. Rollbaserad åtkomstkontroll (RBAC) kan du hantera vilka användare som har behörighet att utföra specifika åtgärder i ett omfång. En roll definierar en uppsättning tillåtna åtgärder. Du tilldela rollen till ett omfång och anger vilka användare som tillhör rollen för detta scope.

När du planerar din strategi för åtkomstkontroll, ge användarna lägsta behörighet för att utföra sitt arbete. Följande bild visar ett föreslagna mönster för att tilldela RBAC.

![Omfång](./media/resource-manager-governance-rbac/role-examples.png)

Det finns tre roller som gäller för alla resurser - ägare, deltagare och läsare. Alla konton som har tilldelats till rollen ägare bör vara hårt styrda och används sällan. Användare som endast behöver se status för lösningar ges rollen läsare.

De flesta användare beviljas [resursspecifika roller](../articles/role-based-access-control/built-in-roles.md) eller [anpassade roller](../articles/role-based-access-control/custom-roles.md) på gruppnivå prenumerationen eller resursen. Rollerna definierar nära tillåtna åtgärder. Genom att tilldela användare till dessa roller, ge den nödvändiga åtkomsten för användare utan tillåter kontroll av för mycket. Du kan tilldela ett konto till mer än en roll, och användaren får kombinerade behörigheterna för rollerna. Bevilja åtkomst på resursnivån är ofta för begränsande för användare, men fungerar för en automatiserad process som utformats för en viss uppgift.

### <a name="who-can-assign-roles"></a>Vem som kan tilldela roller

För att kunna skapa och ta bort rolltilldelningar måste användare ha `Microsoft.Authorization/roleAssignments/*`-åtkomst. Den här åtkomsten beviljas via rollerna Ägare eller Administratör för användaråtkomst.