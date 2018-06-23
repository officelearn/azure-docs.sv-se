---
title: Hantera åtkomst med hjälp av RBAC och REST-API - Azure | Microsoft Docs
description: Lär dig mer om att hantera åtkomst för användare, grupper och program, med hjälp av rollbaserad åtkomstkontroll (RBAC) och REST-API. Detta inkluderar listan åtkomst, bevilja åtkomst och ta bort åtkomst.
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
ms.openlocfilehash: cfcb87fdff8105b25d4f7e63b775aaf9243d2a90
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317015"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Hantera åtkomst med hjälp av RBAC och REST-API

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är på sätt som du hanterar åtkomst till resurser i Azure. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program med RBAC och REST-API.

## <a name="list-access"></a>Listan åtkomst

RBAC anger lista åtkomst du i rolltilldelningar. Vill se rolltilldelningar kan du använda en av de [rolltilldelningar - listan](/rest/api/authorization/roleassignments/list) REST API: er. För att förfina sökningen, anger du ett scope och ett valfritt filter. För att anropa API: et, måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/read` åtgärden i det specificerade omfånget. Flera [inbyggda roller](built-in-roles.md) beviljas åtkomst till den här åtgärden.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Inom URI, Ersätt *{scope}* med den omfattning som du vill visa en lista över rolltilldelningarna.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med villkor som du vill använda för filtrering av listan för tilldelning av rollen.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Lista över rolltilldelningar för endast det angivna omfånget, exklusive rolltilldelningar på subscopes. |
    | `$filter=principalId%20eq%20'{objectId}'` | Lista över rolltilldelningar för en specifik användare, grupp eller tjänstens huvudnamn. |
    | `$filter=assignedTo('{objectId}')` | Lista rolltilldelningar för en viss användare, inklusive de som ärvts från grupper. |

## <a name="grant-access"></a>Bevilja åtkomst

I RBAC, för att bevilja åtkomst, skapar du en rolltilldelning. Så här skapar du en rolltilldelning i [rolltilldelningar - skapa](/rest/api/authorization/roleassignments/create) REST-API och ange säkerhetsobjekt, rolldefinitionen och omfång. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/write` igen. I de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden.

1. Använd den [rolldefinitioner - listan](/rest/api/authorization/roledefinitions/list) REST API: et eller se [inbyggda roller](built-in-roles.md) att hämta identifieraren för den rolldefinition som du vill tilldela.

1. Använda en GUID för att generera en unik identifierare som ska användas för identifieraren för tilldelning av rollen. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Inom URI, Ersätt *{scope}* med omfång för rolltilldelning.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentName}* med GUID-identifierare för rolltilldelningen.

1. I begärandetexten, Ersätt *{subscriptionId}* med prenumerations-ID.

1. Ersätt *{roleDefinitionId}* med identifieraren för rollen.

1. Ersätt *{principalId}* med objekt-ID: t för den användare, grupp eller huvudnamn för tjänsten som ska tilldelas rollen.

## <a name="remove-access"></a>Ta bort åtkomst

I RBAC, för att ta bort access kan du ta bort en rolltilldelning. Ta bort en rolltilldelning med den [rolltilldelningar - ta bort](/rest/api/authorization/roleassignments/delete) REST API. För att anropa denna API, måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/delete` igen. I de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden.

1. Hämta rollen tilldelning identifierare (GUID). Den här identifieraren returneras när du först skapa rolltilldelningen eller du kan hämta genom att ange rolltilldelningarna.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Inom URI, Ersätt *{scope}* med scope för att ta bort rolltilldelningen.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{roleAssignmentName}* med GUID-identifierare för rolltilldelningen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa anpassade roller med hjälp av REST-API](custom-roles-rest.md)