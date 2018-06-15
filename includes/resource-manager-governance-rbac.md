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
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31433257"
---
Du vill kontrollera att användare i din organisation har rätt nivå av åtkomst till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också kontrollera att de kan utföra sitt arbete. Rollbaserad åtkomstkontroll (RBAC) kan du hantera vilka användare som har behörighet att utföra specifika åtgärder på ett scope. En roll definierar en uppsättning tillåtna åtgärder. Du tilldelar rollen till ett scope och ange vilka användare som hör till rollen för scope.

När du planerar din strategi för åtkomstkontroll, bevilja användare minsta behörighet för att utföra sitt arbete. Följande bild visar ett föreslagna mönster för att tilldela RBAC.

![Omfång](./media/resource-manager-governance-rbac/role-examples.png)

Det finns tre roller som gäller för alla resurser - ägare, deltagare och läsare. Alla konton som tilldelas rollen som ägare bör vara hårt styrda och används sällan. Användare som bara behöver se status för lösningar ges rollen läsare.

De flesta användare har beviljats [resursspecifika roller](../articles/role-based-access-control/built-in-roles.md) eller [anpassade roller](../articles/role-based-access-control/custom-roles.md) på prenumerationen eller resursen gruppnivå. Rollerna definiera nära tillåtna åtgärder. Genom att tilldela användare till dessa roller kan bevilja du den nödvändiga åtkomsten för användare utan tillåter kontroll av för mycket. Du kan tilldela ett konto till mer än en roll, och användaren får kombinerade behörigheterna för rollerna. Bevilja åtkomst på nivån resursen är ofta för begränsande för användare, men kan fungera för en automatiserad process som utformats för aktivitet.

### <a name="who-can-assign-roles"></a>Vem som kan tilldela roller

Om du vill skapa och ta bort rolltilldelningar, måste användarna ha `Microsoft.Authorization/roleAssignments/*` åtkomst. Den här komma åt via rollerna ägare eller administratör för användaråtkomst.