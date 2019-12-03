---
title: Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och REST API
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av rollbaserad åtkomst kontroll i Azure (RBAC) och REST API.
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
ms.openlocfilehash: 1ba0c2bd81f32c0aec242dbfb32b2d7f4064ddbe
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707833"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] den här artikeln beskriver hur du tilldelar roller med hjälp av REST API.

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`-och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Lägg till en roll tilldelning

I RBAC för att bevilja åtkomst lägger du till en roll tilldelning. Om du vill lägga till en roll tilldelning använder du [roll tilldelningarna-skapa](/rest/api/authorization/roleassignments/create) REST API och anger säkerhets objekt, roll definition och omfattning. För att anropa detta API måste du ha åtkomst till `Microsoft.Authorization/roleAssignments/write`-åtgärden. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Använd [roll definitionerna-List](/rest/api/authorization/roledefinitions/list) REST API eller se [inbyggda roller](built-in-roles.md) för att hämta identifieraren för den roll definition som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för roll tilldelnings-ID: t. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

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

    | Omfång | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Prenumeration |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för roll tilldelningen.

1. I begär ande texten ersätter du *{scope}* med omfånget för roll tilldelningen.

    | Omfång | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Prenumeration |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med roll Definitions-ID: n.

1. Ersätt *{principalId}* med objekt identifieraren för användaren, gruppen eller tjänstens huvud namn som ska tilldelas rollen.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Om du vill ta bort en roll tilldelning använder du [roll tilldelningarna-ta bort](/rest/api/authorization/roleassignments/delete) REST API. För att anropa detta API måste du ha åtkomst till `Microsoft.Authorization/roleAssignments/delete`-åtgärden. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) åtkomst till den här åtgärden.

1. Hämta roll tilldelnings identifieraren (GUID). Den här identifieraren returneras när du först skapar roll tilldelningen eller så kan du få den genom att lista roll tilldelningarna.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med omfånget för att ta bort roll tilldelningen.

    | Omfång | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    | `subscriptions/{subscriptionId1}` | Prenumeration |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för roll tilldelningen.

## <a name="next-steps"></a>Nästa steg

- [Lista roll tilldelningar med hjälp av Azure RBAC och REST API](role-assignments-list-rest.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa anpassade roller för Azure-resurser med hjälp av REST API](custom-roles-rest.md)
