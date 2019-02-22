---
title: Skapa anpassade roller för Azure-resurser med hjälp av REST-API – Azure | Microsoft Docs
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomstkontroll (RBAC för Azure-resurser med hjälp av REST-API). Detta omfattar att lista, skapa, uppdatera och ta bort anpassade roller.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cec75f757789be4f962cf2b0fbf6b9443a4453cc
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588202"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Skapa anpassade roller för Azure-resurser med hjälp av REST-API

Om den [inbyggda roller för Azure-resurser](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa dina egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av REST-API.

## <a name="list-roles"></a>Visa roller

Om du vill visa en lista över alla roller eller hämta information om en roll med hjälp av dess visningsnamn, använder den [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/read` igen i omfånget. Flera [inbyggda roller](built-in-roles.md) beviljas åtkomst till den här åtgärden.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill visa en lista över rollerna.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med villkor som du vill använda för att filtrera listan med rollen.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Lista över roller som är tillgängliga för tilldelning i det specificerade omfånget eller några av dess underordnade scope. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Använd URL-kodad form av exakta visningsnamnet för rollen. Till exempel `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Få information om en roll

Om du vill ha information om en roll med dess identifierare för definition av rollen kan använda den [rolldefinitioner – hämta](/rest/api/authorization/roledefinitions/get) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/read` igen i omfånget. Flera [inbyggda roller](built-in-roles.md) beviljas åtkomst till den här åtgärden.

Om du vill ha information om en roll med hjälp av dess visningsnamn kan se föregående [lista roller](custom-roles-rest.md#list-roles) avsnittet.

1. Använd den [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) REST API för att hämta GUID-identifierare för rollen. Inbyggda roller, du kan också få identifierare från [inbyggda roller](built-in-roles.md).

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill visa en lista över rollerna.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för rolldefinitionen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Du kan skapa en anpassad roll med den [rolldefinitioner – skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/write` åtgärden på alla de `assignableScopes`. Av de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Granska listan över [åtgärder för resursprovider](resource-provider-operations.md) som är tillgängliga för att skapa behörigheter för din anpassade roll.

1. Använd ett GUID-verktyg för att generera en unik identifierare som används för anpassad roll-ID. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. I URI: N, Ersätt *{omfång}* med först `assignableScopes` av den anpassade rollen.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

1. I begärandetexten, i den `assignableScopes` egenskapen, Ersätt *{roleDefinitionId}* med GUID-identifierare.

1. Ersätt *{subscriptionId}* med ditt prenumerations-ID.

1. I den `actions` egenskapen, lägga till åtgärder som rollen kan utföras.

1. I den `notActions` egenskapen, lägga till åtgärder som är undantagna från den tillåtna `actions`.

1. I den `roleName` och `description` egenskaper, ange ett unikt rollnamn och en beskrivning. Mer information om egenskaperna finns i [anpassade roller](custom-roles.md).

    Nedan visas ett exempel på en brödtext i begäran:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Uppdatera en anpassad roll med den [rolldefinitioner – skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/write` åtgärden på alla de `assignableScopes`. Av de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Använd den [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) eller [rolldefinitioner – hämta](/rest/api/authorization/roledefinitions/get) REST API för att få information om den anpassade rollen. Mer information finns i den tidigare [lista roller](custom-roles-rest.md#list-roles) avsnittet.

1. Börja med följande begäran:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: N, Ersätt *{omfång}* med först `assignableScopes` av den anpassade rollen.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

1. Baserat på informationen om den anpassade rollen kan skapa en brödtext i begäran med följande format:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Uppdatera begärandetexten med ändringar som du vill göra till den anpassade rollen.

    Nedan visas ett exempel på en brödtext i begäran med en ny åtgärd för diagnostikinställningar har lagts till:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Ta bort en anpassad roll genom att använda den [rolldefinitioner – ta bort](/rest/api/authorization/roledefinitions/delete) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/delete` åtgärden på alla de `assignableScopes`. Av de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Använd den [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) eller [rolldefinitioner – hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta GUID-identifierare för den anpassade rollen. Mer information finns i den tidigare [lista roller](custom-roles-rest.md#list-roles) avsnittet.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill ta bort den anpassade rollen.

    | Scope | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)