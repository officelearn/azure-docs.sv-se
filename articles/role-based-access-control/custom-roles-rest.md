---
title: Skapa eller uppdatera anpassade roller för Azure-resurser med REST API
description: Lär dig hur du listar, skapar, uppdaterar eller tar bort anpassade roller med rollbaserad åtkomstkontroll (RBAC) för Azure-resurser med REST API.Learn how to list, create, update, or delete custom roles with role-based access control (RBAC) for Azure resources using the REST API.
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
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062195"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Skapa eller uppdatera anpassade roller för Azure-resurser med REST API

> [!IMPORTANT]
> Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. I den här artikeln beskrivs hur du listar, skapar, uppdaterar eller tar bort anpassade roller med REST-API:et.

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa en lista över alla anpassade roller i en katalog använder du [rolldefinitioner - lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Ersätt *{filter}* med rolltypen.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filter baserat på customrole-typen |

## <a name="list-custom-roles-at-a-scope"></a>Lista anpassade roller i ett scope

Om du vill visa anpassade roller i ett scope använder du [rolldefinitioner - lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{filter}* med rolltypen.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filter baserat på customrole-typen |

## <a name="list-a-custom-role-definition-by-name"></a>Lista en anpassad rolldefinition efter namn

Om du vill ha information om en anpassad roll med dess visningsnamn använder du [rolldefinitionerna - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{filter}* med visningsnamnet för rollen.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Använd den URL-kodade formen för den exakta visningsnamnet för rollen. Till exempel kan`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Lista en anpassad rolldefinition efter ID

Om du vill få information om en anpassad roll med dess unika identifierare använder du [rolldefinitionerna - Get](/rest/api/authorization/roledefinitions/get) REST API.

1. Använd [rolldefinitioner - Lista](/rest/api/authorization/roledefinitions/list) REST API för att hämta GUID-identifieraren för rollen.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa rollerna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resurs |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för rolldefinitionen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om du vill skapa en anpassad roll använder du [rolldefinitionerna - Skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. Om du vill anropa det här API:et måste du vara `Microsoft.Authorization/roleDefinitions/write` inloggad med `assignableScopes`en användare som har tilldelats en roll som har behörighet för alla . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) som innehåller den här behörigheten.

1. Granska listan över [resursprovideråtgärder](resource-provider-operations.md) som är tillgängliga för att skapa behörigheter för din anpassade roll.

1. Använd ett GUID-verktyg för att generera en unik identifierare som ska användas för den anpassade rollidentifieraren. Identifieraren har formatet:`00000000-0000-0000-0000-000000000000`

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Ersätt *{scope} i* URI:n `assignableScopes` med den första av den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. Ersätt *{roleDefinitionId}* i begärantexten med GUID-identifieraren.

1. Om `assignableScopes` är en prenumeration eller resursgrupp ersätter du *instanserna {subscriptionId}* eller *{resourceGroup}* med dina identifierare.

1. Om `assignableScopes` är en hanteringsgrupp ersätter du *instansen {groupId}* med hanteringsgruppsidentifieraren. Att lägga till `assignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.

1. Lägg `actions` till de åtgärder som rollen tillåter att utföras i egenskapen.

1. Lägg `notActions` till de åtgärder som är undantagna `actions`från den tillåtna .

1. Ange `roleName` ett `description` unikt rollnamn och en beskrivning i egenskaperna och i och och. Mer information om egenskaperna finns i [Anpassade roller](custom-roles.md).

    Följande visar ett exempel på en begärantext:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera en anpassad roll använder du [rolldefinitionerna - Skapa eller uppdatera](/rest/api/authorization/roledefinitions/createorupdate) REST API. Om du vill anropa det här API:et måste du vara `Microsoft.Authorization/roleDefinitions/write` inloggad med `assignableScopes`en användare som har tilldelats en roll som har behörighet för alla . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) som innehåller den här behörigheten.

1. Använd [rolldefinitionerna - listan](/rest/api/authorization/roledefinitions/list) eller [rolldefinitionerna - Hämta](/rest/api/authorization/roledefinitions/get) REST API för att få information om den anpassade rollen. Mer information finns i avsnittet tidigare [listroller.](#list-custom-roles)

1. Börja med följande begäran:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Ersätt *{scope} i* URI:n `assignableScopes` med den första av den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

1. Baserat på informationen om den anpassade rollen skapar du en begärandetext med följande format:

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Uppdatera förfrådetexten med de ändringar du vill göra i den anpassade rollen.

    Följande visar ett exempel på en begärandetext med en ny åtgärd för diagnostikinställningar tillagd:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Om du vill ta bort en anpassad roll använder du [rolldefinitionerna - Ta bort](/rest/api/authorization/roledefinitions/delete) REST API. Om du vill anropa det här API:et måste du vara `Microsoft.Authorization/roleDefinitions/delete` inloggad med `assignableScopes`en användare som har tilldelats en roll som har behörighet för alla . Av de inbyggda rollerna är det bara [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) som innehåller den här behörigheten.

1. Använd [rolldefinitionerna - listan](/rest/api/authorization/roledefinitions/list) eller [rolldefinitionerna - Hämta](/rest/api/authorization/roledefinitions/get) REST API för att hämta GUID-identifieraren för den anpassade rollen. Mer information finns i avsnittet tidigare [listroller.](#list-custom-roles)

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill ta bort den anpassade rollen.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Prenumeration |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resursgrupp |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Hanteringsgrupp |

1. Ersätt *{roleDefinitionId}* med GUID-identifieraren för den anpassade rollen.

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
