---
title: Skapa eller uppdatera anpassade roller för Azure-resurser med hjälp av REST API-Azure | Microsoft Docs
description: Lär dig att visa, skapa, uppdatera eller ta bort anpassade roller med rollbaserad åtkomst kontroll (RBAC) för Azure-resurser med hjälp av REST API.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 474de8934ec7e27df601fe80809566a801e6af61
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452927"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Skapa eller uppdatera anpassade roller för Azure-resurser med hjälp av REST API

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Den här artikeln beskriver hur du visar, skapar, uppdaterar eller tar bort anpassade roller med hjälp av REST API.

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista med alla anpassade roller i en katalog använder du REST API [roll definitioner-lista](/rest/api/authorization/roledefinitions/list) .

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersätt *{filter}* med roll typen.

    | Filtrera | Beskrivning |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrera baserat på typen av CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Visa en lista med anpassade roller i ett omfång

Om du vill visa en lista med anpassade roller i ett omfång använder du [roll definitionerna-list](/rest/api/authorization/roledefinitions/list) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med roll typen.

    | Filtrera | Beskrivning |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtrera baserat på typen av CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Lista en anpassad roll definition efter namn

Om du vill hämta information om en anpassad roll med dess visnings namn använder du [roll definitionerna-get](/rest/api/authorization/roledefinitions/get) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med rollens visnings namn.

    | Filtrera | Beskrivning |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Använd URL-kodad form med det exakta visnings namnet för rollen. `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` till exempel |

## <a name="list-a-custom-role-definition-by-id"></a>Lista en anpassad roll definition efter ID

Om du vill hämta information om en anpassad roll efter dess unika identifierare använder du [roll definitionerna-get](/rest/api/authorization/roledefinitions/get) REST API.

1. Använd [roll definitionerna-List](/rest/api/authorization/roledefinitions/list) REST API för att hämta GUID-identifieraren för rollen.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med den omfattning som du vill lista rollerna för.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med roll DEFINITIONens GUID-identifierare.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om du vill skapa en anpassad roll använder du [roll definitionerna-skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa detta API måste du vara inloggad med en användare som har tilldelats en roll som har behörigheten `Microsoft.Authorization/roleDefinitions/write` på alla `assignableScopes`. Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Granska listan över [resurs leverantörs åtgärder](resource-provider-operations.md) som är tillgängliga för att skapa behörigheter för din anpassade roll.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för den anpassade roll identifieraren. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

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

1. I URI: n ersätter du *{scope}* med den första `assignableScopes` av den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. I begär ande texten i egenskapen `assignableScopes` ersätter du *{roleDefinitionId}* med GUID-identifieraren.

1. Ersätt *{subscriptionId}* med ditt prenumerations-ID.

1. I egenskapen `actions` lägger du till de åtgärder som rollen kan utföra.

1. I egenskapen `notActions` lägger du till de åtgärder som undantas från den tillåtna `actions`.

1. I egenskaperna `roleName` och `description` anger du ett unikt roll namn och en beskrivning. Mer information om egenskaperna finns i [anpassade roller](custom-roles.md).

    Följande visar ett exempel på en begär ande text:

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

Om du vill uppdatera en anpassad roll använder du [roll definitionerna-skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. För att anropa detta API måste du vara inloggad med en användare som har tilldelats en roll som har behörigheten `Microsoft.Authorization/roleDefinitions/write` på alla `assignableScopes`. Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Använd [roll definitionerna – lista](/rest/api/authorization/roledefinitions/list) eller [roll definitioner – Hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta information om den anpassade rollen. Mer information finns i avsnittet om [anpassade roller](#list-custom-roles) i föregående lista.

1. Börja med följande begäran:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med den första `assignableScopes` av den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. Utifrån informationen om den anpassade rollen skapar du en begär ande text med följande format:

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

1. Uppdatera begär ande texten med de ändringar som du vill göra till den anpassade rollen.

    Nedan visas ett exempel på en begär ande text med en ny åtgärd för diagnostiska inställningar som har lagts till:

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

Om du vill ta bort en anpassad roll använder du [roll definitionerna-ta bort](/rest/api/authorization/roledefinitions/delete) REST API. För att anropa detta API måste du vara inloggad med en användare som har tilldelats en roll som har behörigheten `Microsoft.Authorization/roleDefinitions/delete` på alla `assignableScopes`. Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [användar åtkomst administratör](built-in-roles.md#user-access-administrator) som har den här behörigheten.

1. Använd [roll definitionerna – lista](/rest/api/authorization/roledefinitions/list) eller [roll definitioner – Hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta GUID-identifieraren för den anpassade rollen. Mer information finns i avsnittet om [anpassade roller](#list-custom-roles) i föregående lista.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill ta bort den anpassade rollen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
