---
title: Visa lista över nekade tilldelningar för Azure-resurser med REST API
description: Lär dig hur du visar en lista över nekade tilldelningar för användare, grupper och program med rollbaserad åtkomst kontroll (RBAC) för Azure-resurser och REST API.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137327"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Visa lista över nekade tilldelningar för Azure-resurser med hjälp av REST API

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger dem åtkomst. Den här artikeln beskriver hur du visar en lista över nekade tilldelningar med hjälp av REST API.

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt. För information om hur neka-tilldelningar skapas, se [neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Förutsättningar

För att få information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read` behörighet, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista en enskild neka-tilldelning

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa neka-tilldelningar för.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{Deny-Assignment-ID}* med det ID för tilldelning av neka som du vill hämta.

## <a name="list-multiple-deny-assignments"></a>Lista flera neka-tilldelningar

1. Börja med en av följande begär Anden:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Med valfria parametrar:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. I URI: n ersätter du *{scope}* med det omfång som du vill visa neka-tilldelningar för.

    | Omfång | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera listan neka tilldelning.

    | Filter | Beskrivning |
    | --- | --- |
    | (inget filter) | Visa alla neka-tilldelningar på, ovanför och under det angivna omfånget. |
    | `$filter=atScope()` | Visa lista över neka-tilldelningar för det angivna omfånget och ovan. Inkluderar inte neka-tilldelningar i underscope. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Visa lista över nekade tilldelningar med det angivna namnet. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Visa lista över neka tilldelningar i rot omfånget (/)

1. Öka åtkomsten enligt beskrivningen i [öka åtkomsten för en global administratör i Azure Active Directory](elevate-access-global-admin.md).

1. Använd följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Ersätt *{filter}* med villkoret som du vill använda för att filtrera listan neka tilldelning. Ett filter måste anges.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Visa lista över neka-tilldelningar för rot omfånget. Inkluderar inte neka-tilldelningar i underscope. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Visa lista över nekade tilldelningar med det angivna namnet. |

1. Ta bort utökad åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Förstå neka-tilldelningar för Azure-resurser](deny-assignments.md)
- [Utöka behörighet för global administratör i Azure Active Directory](elevate-access-global-admin.md)
- [Azure REST API-referens](/rest/api/azure/)
