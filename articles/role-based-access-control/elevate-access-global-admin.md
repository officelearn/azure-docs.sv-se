---
title: Utöka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper
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
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 18490ec7c3cfde33cef186b753e2adc809f854c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641356"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Utöka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper

Som global administratör i Azure Active Directory (Azure AD) kanske du inte har åtkomst till alla prenumerationer och hanterings grupper i din katalog. I den här artikeln beskrivs hur du kan öka åtkomsten till alla prenumerationer och hanterings grupper.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Varför måste du öka din åtkomst?

Om du är global administratör kan det finnas tillfällen när du vill utföra följande åtgärder:

- Få åtkomst till en Azure-prenumeration eller hanterings grupp när en användare har förlorat åtkomst
- tilldela en annan användare eller sig själv en Azure-prenumeration eller -hanteringsgrupp
- Visa alla Azure-prenumerationer eller hanterings grupper i en organisation
- Tillåt en Automation-app (till exempel en fakturerings-eller gransknings app) för att få åtkomst till alla Azure-prenumerationer eller hanterings grupper

## <a name="how-does-elevated-access-work"></a>Hur fungerar utökad åtkomst?

Azure AD och Azure-resurser skyddas fristående från varandra. Det innebär att roll tilldelningar i Azure AD inte beviljar åtkomst till Azure-resurser, och Azure Role-tilldelningar ger inte åtkomst till Azure AD. Men om du är [Global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) i Azure AD kan du tilldela dig själv åtkomst till alla Azure-prenumerationer och hanterings grupper i din katalog. Använd den här funktionen om du inte har åtkomst till Azures prenumerations resurser, till exempel virtuella datorer eller lagrings konton, och du vill använda din globala administratörs behörighet för att få åtkomst till dessa resurser.

När du höjer åtkomsten tilldelas du rollen [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) i Azure vid rot omfånget (`/`).På så sätt kan du Visa alla resurser och tilldela åtkomst i alla prenumerationer eller hanterings grupper i katalogen. Roll tilldelningar för administratör för användar åtkomst kan tas bort med hjälp av Azure PowerShell, Azure CLI eller REST API.

Du bör ta bort den här utökade åtkomsten när du har gjort ändringarna som du behöver göra i rot omfånget.

![Upphöj åtkomst](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomsten för en global administratör

Följ de här stegen för att öka åtkomsten för en global administratör med hjälp av Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrations Center](https://aad.portal.azure.com) som global administratör.

1. Öppna **Azure Active Directory**.

1. Under **Hantera**, väljer du **Egenskaper**.

   ![Välj Egenskaper för Azure Active Directory egenskaper-skärm bild](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Under **åtkomst hantering för Azure-resurser**ställer du in växla till **Ja**.

   ![Åtkomst hantering för Azure-resurser – skärm bild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du ställer in växla till **Ja**tilldelas du rollen administratör för användar åtkomst i Azure RBAC vid rot omfånget (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanterings grupper som är kopplade till den här Azure AD-katalogen. Den här växlingen är bara tillgänglig för användare som har tilldelats rollen som global administratör i Azure AD.

   När du ställer in växla till **Nej**tas rollen administratör för användar åtkomst i Azure RBAC bort från ditt användar konto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanterings grupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanterings grupper som du har beviljats åtkomst till.

    > [!NOTE]
    > Om du använder [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)ändrar du inte växlingen till **Nej**om du inaktiverar roll tilldelningen. Om du vill behålla minst privilegie rad åtkomst rekommenderar vi att du ställer in den här växlingen på **Nej** innan du inaktiverar din roll tilldelning.
    
1. Spara inställningen genom att klicka på **Spara** .

   Den här inställningen är inte en global egenskap och gäller endast för den inloggade användaren. Du kan inte öka åtkomsten för alla medlemmar i rollen global administratör.

1. Logga ut och logga in igen för att uppdatera åtkomsten.

    Du bör nu ha åtkomst till alla prenumerationer och hanterings grupper i din katalog. När du visar fönstret åtkomst kontroll (IAM) ser du att du har tilldelats rollen administratör för användar åtkomst i rot omfånget.

   ![Prenumerations roll tilldelningar med rot omfånget – skärm bild](./media/elevate-access-global-admin/iam-root.png)

1. Gör de ändringar du behöver för att göra den utökade åtkomsten.

    Information om hur du tilldelar roller finns i [Hantera åtkomst med RBAC och Azure Portal](role-assignments-portal.md). Om du använder Azure AD Privileged Identity Management (PIM) kan du läsa [identifiera Azure-resurser som ska hanteras i PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) eller [tilldela Azure-resurs roller i PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort roll tilldelningen administratör`/`för användar åtkomst i rot omfånget ().

1. Logga in som samma användare som användes för att höja åtkomsten.

1. Klicka på **Azure Active Directory** i navigerings listan och klicka sedan på **Egenskaper**.

1. Ange att **åtkomst hantering för Azure-resurser** ska växla tillbaka till **Nej**. Eftersom det här är en inställning per användare måste du vara inloggad som samma användare som användes för att öka åtkomsten.

    Om du försöker ta bort roll tilldelningen administratör för användar åtkomst i fönstret åtkomst kontroll (IAM) visas följande meddelande. Om du vill ta bort roll tilldelningen måste du ställa in växla tillbaka till **Nej** eller använda Azure PowerShell, Azure CLI eller REST API.

    ![Ta bort roll tilldelningar med rot omfånget](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Lista roll tilldelning i rot omfånget (/)

Om du vill visa roll tilldelningen användar åtkomst administratör för en användare vid rot`/`omfånget () använder du kommandot [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort roll tilldelningen administratör för användar åtkomst för dig`/`själv eller en annan användare vid rot omfånget ().

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som användes för att höja åtkomsten eller en annan global administratör med utökad åtkomst i rot omfånget.

1. Använd kommandot [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) för att ta bort roll tilldelningen administratör för användar åtkomst.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>Lista roll tilldelning i rot omfånget (/)

Om du vill visa roll tilldelningen användar åtkomst administratör för en användare vid rot`/`omfånget () använder du kommandot [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list) .

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

Följ dessa steg om du vill ta bort roll tilldelningen administratör för användar åtkomst för dig`/`själv eller en annan användare vid rot omfånget ().

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som användes för att höja åtkomsten eller en annan global administratör med utökad åtkomst i rot omfånget.

1. Använd kommandot [AZ Role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete) för att ta bort roll tilldelningen administratör för användar åtkomst.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Utöka åtkomsten för en global administratör

Använd följande grundläggande steg för att öka åtkomsten för en global administratör med hjälp av REST API.

1. Med REST, anrop `elevateAccess`, som ger dig rollen som administratör för användar åtkomst i rot omfånget (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Skapa en [roll tilldelning](/rest/api/authorization/roleassignments) för att tilldela en roll i valfri omfattning. I följande exempel visas egenskaperna för att tilldela rollen {roleDefinitionID} i rot omfånget`/`():

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. En administratör för användar åtkomst kan också ta bort roll tilldelningar i rot omfånget (`/`).

1. Ta bort administratörs behörigheterna för användar åtkomst tills de behövs igen.

### <a name="list-role-assignments-at-root-scope-"></a>Lista roll tilldelningar i rot omfånget (/)

Du kan visa en lista över alla roll tilldelningar för en användare i rot`/`omfånget ().

- Anropa [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) där `{objectIdOfUser}` är objekt-ID: t för den användare vars roll tilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Visa lista över neka tilldelningar i rot omfånget (/)

Du kan visa en lista över alla neka-tilldelningar för en användare i`/`rot omfånget ().

- Anropa GET denyAssignments där `{objectIdOfUser}` är objekt-ID: t för den användare vars neka-tilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

När du anropar `elevateAccess`skapar du en roll tilldelning för dig själv, så att du kan återkalla de behörigheterna som krävs för att ta bort roll tilldelningen administratör för användar`/`åtkomst för sig själv vid rot omfånget ().

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

1. Du måste också ange roll tilldelningen för katalog administratören i katalog omfånget. Visa en lista med alla tilldelningar i `principalId` katalog omfånget för katalog administratören som gjorde höjningen av åtkomst anropet. Visar alla tilldelningar i katalogen för ObjectID.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >En katalog administratör bör inte ha många tilldelningar, om föregående fråga returnerar för många tilldelningar, kan du även fråga efter alla tilldelningar precis på katalogens omfattnings nivå och sedan filtrera resultaten:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. Föregående anrop returnerar en lista över roll tilldelningar. Hitta roll tilldelningen där omfånget `"/"` är och `roleDefinitionId` slutar med det roll namn-ID som du hittade i steg `principalId` 1 och matchar katalog administratörens ObjectID. 
    
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
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Spara sedan ID: t från `name` parametern, i det här fallet 11111111-1111-1111-1111-111111111111.

1. Använd slutligen roll tilldelnings-ID: t för att ta bort `elevateAccess`tilldelningen som lagts till av:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå de olika rollerna i Azure](rbac-and-directory-admin-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
