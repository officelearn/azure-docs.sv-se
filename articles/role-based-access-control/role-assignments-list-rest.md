---
title: Lista roll tilldelningar med hjälp av Azure RBAC och REST API
description: Lär dig hur du avgör vilka resurser användare, grupper, tjänstens huvud namn eller hanterade identiteter har åtkomst till med hjälp av rollbaserad åtkomst kontroll i Azure (RBAC) och REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e20edcb5e2406c216711a2e0f696ef06e19fe21e
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710404"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lista roll tilldelningar med hjälp av Azure RBAC och REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] den här artikeln beskriver hur du visar roll tilldelningar med hjälp av REST API.

## <a name="list-role-assignments"></a>Lista roll tilldelningar

I RBAC, för att lista åtkomst, listas roll tilldelningarna. Om du vill lista roll tilldelningar använder du en av REST-API: erna för [roll tilldelningar](/rest/api/authorization/roleassignments/list) . Du kan förfina resultaten genom att ange ett omfång och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa roll tilldelningarna för.

    | Omfång | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Prenumeration |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är Microsoft. Web en resurs leverantör som refererar till en App Service instans. På samma sätt kan du använda andra resurs leverantörer och ange omfånget. Mer information finns i [Azure Resource providers och typer](../azure-resource-manager/resource-manager-supported-services.md) som stöds [Azure Resource Manager Resource Provider-åtgärder](resource-provider-operations.md).  
     
1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera roll tilldelnings listan.

    | Filtrera | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Visar endast roll tilldelningar för det angivna omfånget, inte inklusive roll tilldelningarna i underscope. |
    | `$filter=principalId%20eq%20'{objectId}'` | Visar en lista över roll tilldelningar för en viss användare, grupp eller tjänstens huvud namn. |
    | `$filter=assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för en viss användare eller tjänstens huvud namn. Om användaren är medlem i en grupp som har en roll tilldelning visas även den roll tilldelningen. Det här filtret är transitivt för grupper, vilket innebär att om användaren är medlem i en grupp och gruppen är medlem i en annan grupp som har en roll tilldelning visas även roll tilldelningen. Filtret accepterar bara ett objekt-ID för en användare eller ett tjänst objekt. Det går inte att skicka ett objekt-ID för en grupp. |

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
