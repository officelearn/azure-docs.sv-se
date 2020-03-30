---
title: Lägga till eller ta bort rolltilldelningar med RBAC och REST API
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av Azure Role-based Access Control (RBAC) och REST API.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063001"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lägga till eller ta bort rolltilldelningar med Azure RBAC och REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]I den här artikeln beskrivs hur du tilldelar roller med REST API.

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort rolltilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I RBAC, för att bevilja åtkomst, lägger du till en rolltilldelning. Om du vill lägga till en rolltilldelning använder du [rolltilldelningarna - Skapa](/rest/api/authorization/roleassignments/create) REST API och ange säkerhetsobjekt, rolldefinition och scope. Om du vill anropa det här `Microsoft.Authorization/roleAssignments/write` API:et måste du ha åtkomst till åtgärden. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) åtkomst åtkomst till den här åtgärden.

1. Använd [rolldefinitioner - Lista](/rest/api/authorization/roledefinitions/list) REST API eller se [Inbyggda roller](built-in-roles.md) för att hämta identifieraren för den rolldefinition som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för rolltilldelningsidentifieraren. Identifieraren har formatet:`00000000-0000-0000-0000-000000000000`

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

1. Inom URI:n ersätter *du {scope}* med omfånget för rolltilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

    I föregående exempel är microsoft.web en resursleverantör som refererar till en App Service-instans. På samma sätt kan du använda andra resursleverantörer och ange omfånget. Mer information finns i [Azure Resource-providers och -typer](../azure-resource-manager/management/resource-providers-and-types.md) och som stöds av [Azure Resource Manager-resursprovideråtgärder](resource-provider-operations.md).  

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för rolltilldelningen.

1. Ersätt *{scope}* i brödtexten för begäran med omfånget för rolltilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med rolldefinitionsidentifieraren.

1. Ersätt *{principalId}* med objektidentifieraren för användaren, gruppen eller tjänsthuvudhuvudet som ska tilldelas rollen.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Om du vill ta bort en rolltilldelning använder du [rolltilldelningarna - Ta bort](/rest/api/authorization/roleassignments/delete) REST API. Om du vill anropa det här `Microsoft.Authorization/roleAssignments/delete` API:et måste du ha åtkomst till åtgärden. Av de inbyggda rollerna beviljas endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) åtkomst åtkomst till den här åtgärden.

1. Hämta rolltilldelningsidentifieraren (GUID). Den här identifieraren returneras när du först skapar rolltilldelningen eller så kan du hämta den genom att ange rolltilldelningarna.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. I URI:et ersätter *du {scope}* med omfånget för att ta bort rolltilldelningen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentName}* med GUID-identifieraren för rolltilldelningen.

## <a name="next-steps"></a>Nästa steg

- [Lista rolltilldelningar med Azure RBAC och REST API](role-assignments-list-rest.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa anpassade roller för Azure-resurser med REST API](custom-roles-rest.md)
