---
title: Utöka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper | Microsoft Docs
description: Beskriver hur du höjer åtkomsten för en global administratör för att hantera alla prenumerationer och hanterings grupper i Azure Active Directory att använda Azure Portal eller REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 984284fa185d4d8454b1689a62ca9e08c342e33b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195113"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Utöka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper

Som global administratör i Azure Active Directory (Azure AD) kanske du inte har åtkomst till alla prenumerationer och hanterings grupper i din katalog. I den här artikeln beskrivs hur du kan öka åtkomsten till alla prenumerationer och hanterings grupper.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Varför måste du öka din åtkomst?

Om du är global administratör kan det finnas tillfällen när du vill göra följande:

- Få åtkomst till en Azure-prenumeration eller hanterings grupp när en användare har förlorat åtkomst
- Bevilja en annan användare eller egen åtkomst till en Azure-prenumeration eller hanterings grupp
- Visa alla Azure-prenumerationer eller hanterings grupper i en organisation
- Tillåt en Automation-app (till exempel en fakturerings-eller gransknings app) för att få åtkomst till alla Azure-prenumerationer eller hanterings grupper

## <a name="how-does-elevate-access-work"></a>Hur fungerar höjningen av åtkomst?

Azure AD och Azure-resurser skyddas fristående från varandra. Det innebär att roll tilldelningar i Azure AD inte beviljar åtkomst till Azure-resurser, och Azure Role-tilldelningar ger inte åtkomst till Azure AD. Men om du är [Global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) i Azure AD kan du tilldela dig själv åtkomst till alla Azure-prenumerationer och hanterings grupper i din katalog. Använd den här funktionen om du inte har åtkomst till Azures prenumerations resurser, till exempel virtuella datorer eller lagrings konton, och du vill använda din globala administratörs behörighet för att få åtkomst till dessa resurser.

När du höjer åtkomsten tilldelas du rollen [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) i Azure vid rot omfånget (`/`). På så sätt kan du Visa alla resurser och tilldela åtkomst i alla prenumerationer eller hanterings grupper i katalogen. Roll tilldelningar för användar åtkomst administratör kan tas bort med hjälp av PowerShell.

Du bör ta bort den här utökade åtkomsten när du har gjort ändringarna som du behöver göra i rot omfånget.

![Öka åtkomsten](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Följ de här stegen för att öka åtkomsten för en global administratör med hjälp av Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrations Center](https://aad.portal.azure.com) som global administratör.

1. Klicka på **Azure Active Directory** i navigerings listan och klicka sedan på **Egenskaper**.

   ![Egenskaper för Azure AD – skärm bild](./media/elevate-access-global-admin/aad-properties.png)

1. Under **åtkomst hantering för Azure-resurser**ställer du in växla till **Ja**.

   ![Åtkomst hantering för Azure-resurser – skärm bild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du ställer in växla till **Ja**tilldelas du rollen administratör för användar åtkomst i Azure RBAC i rot omfånget (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanterings grupper som är kopplade till den här Azure AD-katalogen. Den här växlingen är bara tillgänglig för användare som har tilldelats rollen som global administratör i Azure AD.

   När du ställer in växla till **Nej**tas rollen administratör för användar åtkomst i Azure RBAC bort från ditt användar konto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanterings grupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanterings grupper som du har beviljats åtkomst till.

1. Spara inställningen genom att klicka på **Spara** .

   Den här inställningen är inte en global egenskap och gäller endast för den inloggade användaren. Du kan inte öka åtkomsten för alla medlemmar i rollen global administratör.

1. Logga ut och logga in igen för att uppdatera åtkomsten.

    Du bör nu ha åtkomst till alla prenumerationer och hanterings grupper i din katalog. Du ser att du har tilldelats rollen som administratör för användar åtkomst i rot omfånget.

   ![Prenumerations roll tilldelningar med rot omfånget – skärm bild](./media/elevate-access-global-admin/iam-root.png)

1. Gör de ändringar du behöver för att göra den utökade åtkomsten.

    Information om hur du tilldelar roller finns i [Hantera åtkomst med RBAC och Azure Portal](role-assignments-portal.md). Om du använder Azure AD Privileged Identity Management (PIM) kan du läsa [identifiera Azure-resurser som ska hanteras i PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) eller [tilldela Azure-resurs roller i PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. När du är färdig ställer du in **åtkomst hanteringen för Azure-resurser** på **Nej**. Eftersom det här är en inställning per användare måste du vara inloggad som samma användare som användes för att öka åtkomsten.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Lista roll tilldelningen vid rot omfånget (/)

Om du vill visa roll tilldelningen användar åtkomst administratör för en användare vid rot omfånget (`/`) använder du kommandot [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Ta bort en roll tilldelning i rot omfånget (/)

Följ dessa steg om du vill ta bort roll tilldelningen administratör för användar åtkomst för`/`en användare i rot omfånget ().

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som användes för att höja åtkomsten eller en annan global administratör med utökad åtkomst i rot omfånget.


1. Använd kommandot [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) för att ta bort roll tilldelningen administratör för användar åtkomst.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomsten för en global administratör

Använd följande grundläggande steg för att öka åtkomsten för en global administratör med hjälp av REST API.

1. Med rest, anrop `elevateAccess`, som ger dig rollen som administratör för användar åtkomst i rot omfånget (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Skapa en [roll tilldelning](/rest/api/authorization/roleassignments) för att tilldela en roll i valfri omfattning. I följande exempel visas egenskaperna för att tilldela rollen {roleDefinitionID} i rot omfånget (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. En administratör för användar åtkomst kan också ta bort roll tilldelningar i rot omfånget`/`().

1. Ta bort administratörs behörigheterna för användar åtkomst tills de behövs igen.

### <a name="list-role-assignments-at-the-root-scope-"></a>Lista roll tilldelningar vid rot omfånget (/)

Du kan visa en lista över alla roll tilldelningar för en användare i rot omfånget (`/`).

- Anropa [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) där `{objectIdOfUser}` är objekt-ID: t för den användare vars roll tilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Visa lista över neka tilldelningar i rot omfånget (/)

Du kan visa en lista över alla neka-tilldelningar för en användare vid rot`/`omfånget ().

- Anropa get denyAssignments där `{objectIdOfUser}` är objekt-ID: t för den användare vars neka-tilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

När du anropar `elevateAccess`skapar du en roll tilldelning för dig själv, så att du kan återkalla de behörigheterna som krävs för att ta bort tilldelningen.

1. Anropa [Get roleDefinitions](/rest/api/authorization/roledefinitions/get) där `roleName` är lika med administratör för användar åtkomst för att fastställa namn-ID för administratörs rollen för användar åtkomst.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Spara ID: t från `name` parametern, i det här `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`fallet.

2. Du måste också ange roll tilldelningen för katalog administratören i katalog omfånget. Visa en lista med alla tilldelningar i `principalId` katalog omfånget för katalog administratören som gjorde höjningen av åtkomst anropet. Visar alla tilldelningar i katalogen för ObjectID.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >En katalog administratör bör inte ha många tilldelningar, om föregående fråga returnerar för många tilldelningar, kan du även fråga efter alla tilldelningar precis på katalogens omfattnings nivå och sedan filtrera resultaten:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. Föregående anrop returnerar en lista över roll tilldelningar. Hitta roll tilldelningen där omfånget `"/"` är `roleDefinitionId` och slutar med det roll namn-ID som du hittade i steg `principalId` 1 och matchar katalog administratörens ObjectID. 
    
      Exempel roll tilldelning:

       ```json
       {
         "value": [
           {
             "properties": {
               "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
               "principalId": "{objectID}",
               "scope": "/",
               "createdOn": "2016-08-17T19:21:16.3422480Z",
               "updatedOn": "2016-08-17T19:21:16.3422480Z",
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      Spara sedan ID: t från `name` parametern, i det här fallet e7dd75bc-06F6-4E71-9014-ee96a929d099.

   1. Använd slutligen roll tilldelnings-ID: t för att ta bort `elevateAccess`tilldelningen som lagts till av:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>Nästa steg

- [Förstå de olika rollerna i Azure](rbac-and-directory-admin-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
