---
title: Hantera åtkomst till Azure-resurser med RBAC och REST API-Azure | Microsoft Docs
description: Lär dig hur du hanterar åtkomst till Azure-resurser för användare, grupper och program med rollbaserad åtkomst kontroll (RBAC) och REST API. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996476"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Hantera åtkomst till Azure-resurser med RBAC och REST API

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program som använder RBAC och REST API.

## <a name="list-access"></a>Visar åtkomst

I RBAC, för att lista åtkomst, listas roll tilldelningarna. Om du vill lista roll tilldelningar använder du en av REST-API: erna för [roll tilldelningar](/rest/api/authorization/roleassignments/list) . Du kan förfina resultaten genom att ange ett omfång och ett valfritt filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa roll tilldelningarna för.

    | Omfång | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    I föregående exempel är Microsoft. Web en resurs leverantör som refererar till en App Service instans. På samma sätt kan du använda andra resurs leverantörer och ange omfånget. Mer information finns i [Azure Resource providers och typer](../azure-resource-manager/resource-manager-supported-services.md) som stöds [Azure Resource Manager Resource Provider-åtgärder](resource-provider-operations.md).  
     
1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera roll tilldelnings listan.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Visar endast roll tilldelningar för det angivna omfånget, inte inklusive roll tilldelningarna i underscope. |
    | `$filter=principalId%20eq%20'{objectId}'` | Visar en lista över roll tilldelningar för en viss användare, grupp eller tjänstens huvud namn. |
    | `$filter=assignedTo('{objectId}')` | Visar en lista över roll tilldelningar för en viss användare eller tjänstens huvud namn. Om användaren är medlem i en grupp som har en roll tilldelning visas även den roll tilldelningen. Det här filtret är transitivt för grupper, vilket innebär att om användaren är medlem i en grupp och gruppen är medlem i en annan grupp som har en roll tilldelning visas även roll tilldelningen. Filtret accepterar bara ett objekt-ID för en användare eller ett tjänst objekt. Det går inte att skicka ett objekt-ID för en grupp. |

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar. Om du vill skapa en roll tilldelning använder du [roll tilldelningarna-skapa](/rest/api/authorization/roleassignments/create) REST API och anger säkerhets objekt, roll definition och omfattning. Du måste ha åtkomst till `Microsoft.Authorization/roleAssignments/write` åtgärden för att anropa detta API. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Använd [roll definitionerna-List](/rest/api/authorization/roledefinitions/list) REST API eller se [inbyggda roller](built-in-roles.md) för att hämta identifieraren för den roll definition som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för roll tilldelnings-ID: t. Identifieraren har formatet:`00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. I URI: n ersätter du *{scope}* med omfånget för roll tilldelningen.

    | Omfång | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för roll tilldelningen.

1. I begär ande texten ersätter du *{scope}* med omfånget för roll tilldelningen.

    | Omfång | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Ersätt *{roleDefinitionId}* med roll Definitions-ID: n.

1. Ersätt *{principalId}* med objekt identifieraren för användaren, gruppen eller tjänstens huvud namn som ska tilldelas rollen.

## <a name="remove-access"></a>Ta bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Om du vill ta bort en roll tilldelning använder du [roll tilldelningarna-ta bort](/rest/api/authorization/roleassignments/delete) REST API. Du måste ha åtkomst till `Microsoft.Authorization/roleAssignments/delete` åtgärden för att anropa detta API. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Hämta roll tilldelnings identifieraren (GUID). Den här identifieraren returneras när du först skapar roll tilldelningen eller så kan du få den genom att lista roll tilldelningarna.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med omfånget för att ta bort roll tilldelningen.

    | Omfång | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för roll tilldelningen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa anpassade roller för Azure-resurser med hjälp av REST API](custom-roles-rest.md)
