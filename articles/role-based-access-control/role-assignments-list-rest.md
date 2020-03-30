---
title: Lista rolltilldelningar med Azure RBAC och REST API
description: Lär dig hur du tar reda på vilka resurser användare, grupper, tjänsthuvudnamn eller hanterade identiteter har åtkomst till med hjälp av Azure-rollbaserad åtkomstkontroll (RBAC) och REST API.Learn how to determine what resources users, groups, service principals, or managed identities have access to using Azure Role-based access control (RBAC) and the REST API.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062164"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lista rolltilldelningar med Azure RBAC och REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]I den här artikeln beskrivs hur du listar rolltilldelningar med REST API.

> [!NOTE]
> Om din organisation har lagt ut hanteringsfunktioner på entreprenad till en tjänsteleverantör som använder [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md)visas inte rolltilldelningar som auktoriserats av den tjänsteleverantören här.

## <a name="list-role-assignments"></a>Visa lista över rolltilldelningar

I RBAC, för att lista åtkomst, listar du rolltilldelningarna. Om du vill visa rolltilldelningar använder du en av [rolltilldelningarna - Lista](/rest/api/authorization/roleassignments/list) REST API:er. Om du vill förfina resultaten anger du ett scope och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rolltilldelningarna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

    I föregående exempel är microsoft.web en resursleverantör som refererar till en App Service-instans. På samma sätt kan du använda andra resursleverantörer och ange omfånget. Mer information finns i [Azure Resource-providers och -typer](../azure-resource-manager/management/resource-providers-and-types.md) och som stöds av [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md).  
     
1. Ersätt *{filter}* med det villkor som du vill använda för att filtrera rolltilldelningslistan.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=atScope()` | Visar rolltilldelningar för endast det angivna scopet, exklusive rolltilldelningar i underscope. |
    > | `$filter=assignedTo('{objectId}')` | Visar rolltilldelningar för ett angivet användar- eller tjänsthuvudnamn.<br/>Om användaren är medlem i en grupp som har en rolltilldelning visas även den rolltilldelningen. Det här filtret är transitivt för grupper vilket innebär att om användaren är medlem i en grupp och den gruppen är medlem i en annan grupp som har en rolltilldelning, visas även den rolltilldelningen.<br/>Det här filtret accepterar bara ett objekt-ID för en användare eller ett tjänsthuvudnamn. Du kan inte skicka ett objekt-ID för en grupp. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Visar rolltilldelningar för det angivna användarens eller tjänstens huvudnamn och vid det angivna scopet. |
    > | `$filter=principalId+eq+'{objectId}'` | Visar rolltilldelningar för en angiven användare, grupp eller tjänsthuvudnamn. |

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort rolltilldelningar med Azure RBAC och REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
