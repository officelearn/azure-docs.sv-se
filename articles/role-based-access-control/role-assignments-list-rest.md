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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062164"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lista roll tilldelningar med hjälp av Azure RBAC och REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Den här artikeln beskriver hur du visar roll tilldelningar med hjälp av REST API.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="list-role-assignments"></a>Visa lista över rolltilldelningar

I RBAC, för att lista åtkomst, listas roll tilldelningarna. Om du vill lista roll tilldelningar använder du en av REST-API: erna för [roll tilldelningar](/rest/api/authorization/roleassignments/list) . Du kan förfina resultaten genom att ange ett omfång och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa roll tilldelningarna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är Microsoft. Web en resurs leverantör som refererar till en App Service instans. På samma sätt kan du använda andra resurs leverantörer och ange omfånget. Mer information finns i [Azure Resource providers och typer](../azure-resource-manager/management/resource-providers-and-types.md) som stöds [Azure Resource Manager Resource Provider-åtgärder](resource-provider-operations.md).  
     
1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera roll tilldelnings listan.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=atScope()` | Visar endast roll tilldelningar för det angivna omfånget, inte inklusive roll tilldelningarna i underscope. |
    > | `$filter=assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för en viss användare eller tjänstens huvud namn.<br/>Om användaren är medlem i en grupp som har en roll tilldelning visas även den roll tilldelningen. Det här filtret är transitivt för grupper, vilket innebär att om användaren är medlem i en grupp och gruppen är medlem i en annan grupp som har en roll tilldelning visas även roll tilldelningen.<br/>Filtret accepterar bara ett objekt-ID för en användare eller ett tjänst objekt. Det går inte att skicka ett objekt-ID för en grupp. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för den angivna användaren eller tjänstens huvud namn och vid det angivna omfånget. |
    > | `$filter=principalId+eq+'{objectId}'` | Visar en lista över roll tilldelningar för en viss användare, grupp eller tjänstens huvud namn. |

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och REST API](role-assignments-rest.md)
- [Referens för Azure-REST API](/rest/api/azure/)
