---
title: Skapa anpassade roller med hjälp av REST API - Azure | Microsoft Docs
description: Lär dig hur du skapar anpassade roller för rollbaserad åtkomstkontroll (RBAC) med hjälp av REST-API. Detta innefattar hur du visa, skapa, uppdatera och ta bort anpassade roller.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8267846fed30baf2c37dcddd453ae9ead9341da9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320592"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Skapa anpassade roller med hjälp av REST-API

Om den [inbyggda roller](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av REST API.

## <a name="list-roles"></a>Lista roller

Om du vill visa en lista över alla roller eller hämta information om en roll med hjälp av dess namn, Använd den [rolldefinitioner - listan](/rest/api/authorization/roledefinitions/list) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/read` åtgärden definitionsområdet. Flera [inbyggda roller](built-in-roles.md) beviljas åtkomst till den här åtgärden.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Inom URI, Ersätt *{scope}* med den omfattning som du vill visa en lista över roller.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med villkor som du vill använda för att filtrera listan roll.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Lista över roller som är tillgängliga för tilldelning i det specificerade omfånget och alla dess underordnade omfång. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Använd URL-kodade formatet exakt visningsnamnet för rollen. Till exempel `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Hämta information om en roll

Få information om en roll med dess identifierare för definition av rollen med hjälp av [rolldefinitioner - hämta](/rest/api/authorization/roledefinitions/get) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/read` åtgärden definitionsområdet. Flera [inbyggda roller](built-in-roles.md) beviljas åtkomst till den här åtgärden.

För att få information om en roll med hjälp av dess namn, se föregående [lista roller](custom-roles-rest.md#list-roles) avsnitt.

1. Använd den [rolldefinitioner - listan](/rest/api/authorization/roledefinitions/list) REST API för att hämta GUID-identifierare för rollen. Inbyggda roller, du kan också få identifierare från [inbyggda roller](built-in-roles.md).

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Inom URI, Ersätt *{scope}* med den omfattning som du vill visa en lista över roller.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för rolldefinitionen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Du kan skapa en anpassad roll med den [rolldefinitioner – skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/write` igen på alla de `assignableScopes`. I de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Granska listan över [resource provider operations](resource-provider-operations.md) som är tillgängliga skapar behörigheter för en anpassad roll.

1. Verktyget GUID för att generera en unik identifierare som används för anpassad roll-identifierare. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

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

1. Inom URI, Ersätt *{scope}* med först `assignableScopes` för den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

1. I begärandetexten, i den `assignableScopes` egenskapen ersätta *{roleDefinitionId}* med GUID-identifierare.

1. Ersätt *{subscriptionId}* med prenumerations-ID.

1. I den `actions` egenskap, lägga till de åtgärder som rollen kan utföras.

1. I den `notActions` egenskap, lägga till de åtgärder som är undantagna från den tillåtna `actions`.

1. I den `roleName` och `description` egenskaper, ange ett unikt rollnamn och en beskrivning. Mer information om egenskaperna som finns [anpassade roller](custom-roles.md).

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

Uppdatera en anpassad roll med den [rolldefinitioner – skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/write` igen på alla de `assignableScopes`. I de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Använd den [rolldefinitioner - listan](/rest/api/authorization/roledefinitions/list) eller [rolldefinitioner - hämta](/rest/api/authorization/roledefinitions/get) REST API för att få information om den anpassade rollen. Mer information finns i den tidigare [lista roller](custom-roles-rest.md#list-roles) avsnitt.

1. Börja med följande begäran:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Inom URI, Ersätt *{scope}* med först `assignableScopes` för den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

1. Baserat på information om den anpassade rollen, skapa en brödtext i begäran med följande format:

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

1. Uppdatera begärandetexten med ändringar som du vill kontrollera att den anpassade rollen.

    Nedan visas ett exempel på en brödtext i begäran med en ny diagnostikinställningar åtgärd som lagts till:

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

Ta bort en anpassad roll genom att använda den [rolldefinitioner - ta bort](/rest/api/authorization/roledefinitions/delete) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleDefinitions/delete` igen på alla de `assignableScopes`. I de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden. 

1. Använd den [rolldefinitioner - listan](/rest/api/authorization/roledefinitions/list) eller [rolldefinitioner - hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta GUID-identifierare för den anpassade rollen. Mer information finns i den tidigare [lista roller](custom-roles-rest.md#list-roles) avsnitt.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Inom URI, Ersätt *{scope}* med det omfång som du vill ta bort den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifierare för den anpassade rollen.

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller i Azure](custom-roles.md)
- [Hantera åtkomst med hjälp av RBAC och REST-API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)