---
title: Lista neka tilldelningar för Azure-resurser med REST API
description: Lär dig hur du listar neka tilldelningar för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) för Azure-resurser och REST API.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063022"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista neka tilldelningar för Azure-resurser med REST API

[Neka tilldelningar](deny-assignments.md) blockera användare från att utföra specifika Azure-resursåtgärder även om en rolltilldelning ger dem åtkomst. I den här artikeln beskrivs hur du listar neka tilldelningar med REST API.

> [!NOTE]
> Du kan inte direkt skapa dina egna neka-tilldelningar. Information om hur neka tilldelningar skapas finns i [Neka tilldelningar](deny-assignments.md).

## <a name="prerequisites"></a>Krav

Om du vill ha information om en neka-tilldelning måste du ha:

- `Microsoft.Authorization/denyAssignments/read`behörighet, som ingår i de flesta [inbyggda roller för Azure-resurser](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista en enda neka tilldelning

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa neka-tilldelningarna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Prenumeration |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{neka-tilldelning-id}* med den neka tilldelningsidentifierare som du vill hämta.

## <a name="list-multiple-deny-assignments"></a>Lista flera neka tilldelningar

1. Börja med en av följande begäranden:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Med valfria parametrar:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. I URI:et ersätter du *{scope}* med det scope som du vill visa neka-tilldelningarna för.

    > [!div class="mx-tableFixed"]
    > | Omfång | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Prenumeration |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resurs |

1. Ersätt *{filter}* med det villkor som du vill använda för att filtrera listan över neka tilldelning.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | (inget filter) | Visar alla neka tilldelningar på, ovanför och under det angivna scopet. |
    > | `$filter=atScope()` | Visar listor som nekar tilldelningar för endast det angivna scopet och högre. Omfattar inte neka tilldelningar på underscope. |
    > | `$filter=assignedTo('{objectId}')` | Visar listor som nekar tilldelningar för det angivna användar- eller tjänsthuvudhuvudet.<br/>Om användaren är medlem i en grupp som har en nekad tilldelning visas även den neka tilldelningen. Det här filtret är transitivt för grupper vilket innebär att om användaren är medlem i en grupp och den gruppen är medlem i en annan grupp som har en nekad tilldelning, visas även tilldelningen.<br/>Det här filtret accepterar bara ett objekt-ID för en användare eller ett tjänsthuvudnamn. Du kan inte skicka ett objekt-ID för en grupp. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Visar listor som nekar tilldelningar för det angivna användarens eller tjänstens huvudnamn och i det angivna scopet. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Visar listor som nekar tilldelningar med det angivna namnet. |
    > | `$filter=principalId+eq+'{objectId}'` | Visar listor som nekar tilldelningar för den angivna användaren, gruppen eller tjänstens huvudnamn. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista neka tilldelningar i rotomfånget (/)

1. Öka din åtkomst enligt beskrivningen i [Öka åtkomsten för en global administratör i Azure Active Directory](elevate-access-global-admin.md).

1. Använd följande begäran:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Ersätt *{filter}* med det villkor som du vill använda för att filtrera listan över neka tilldelning. Ett filter krävs.

    > [!div class="mx-tableFixed"]
    > | Filter | Beskrivning |
    > | --- | --- |
    > | `$filter=atScope()` | Lista neka tilldelningar för endast rotomfånget. Omfattar inte neka tilldelningar på underscope. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lista neka tilldelningar med det angivna namnet. |

1. Ta bort förhöjd åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Förstå neka tilldelningar för Azure-resurser](deny-assignments.md)
- [Utöka behörighet för global administratör i Azure Active Directory](elevate-access-global-admin.md)
- [Azure REST API-referens](/rest/api/azure/)
