---
title: Öka åtkomsten för att hantera alla Azure-prenumerationer och hanteringsgrupper
description: Beskriver hur du höjer åtkomsten för en global administratör för att hantera alla prenumerationer och hanteringsgrupper i Azure Active Directory med hjälp av Azure-portalen eller REST API.
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
ms.date: 12/03/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1731c630cb98ac542ebcdc7aedf07f7bb63eaec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137467"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Öka åtkomsten för att hantera alla Azure-prenumerationer och hanteringsgrupper

Som global administratör i Azure Active Directory (Azure AD) kanske du inte har åtkomst till alla prenumerationer och hanteringsgrupper i katalogen. I den här artikeln beskrivs hur du kan öka din åtkomst till alla prenumerationer och hanteringsgrupper.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Varför skulle du behöva höja din tillgång?

Om du är global administratör kan det finnas tillfällen då du vill göra följande:

- Återfå åtkomsten till en Azure-prenumeration eller hanteringsgrupp när en användare har förlorat åtkomst
- tilldela en annan användare eller sig själv en Azure-prenumeration eller -hanteringsgrupp
- Visa alla Azure-prenumerationer eller hanteringsgrupper i en organisation
- Tillåt att en automatiseringsapp (till exempel en fakturerings- eller granskningsapp) har åtkomst till alla Azure-prenumerationer eller hanteringsgrupper

## <a name="how-does-elevated-access-work"></a>Hur fungerar förhöjd åtkomst?

Azure AD- och Azure-resurser skyddas oberoende av varandra. Det vill än azure AD-rolltilldelningar ger inte åtkomst till Azure-resurser och Azure-rolltilldelningar ger inte åtkomst till Azure AD. Men om du är [global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) i Azure AD kan du tilldela dig själv åtkomst till alla Azure-prenumerationer och hanteringsgrupper i din katalog. Använd den här funktionen om du inte har åtkomst till Azure-prenumerationsresurser, till exempel virtuella datorer eller lagringskonton, och du vill använda behörigheten Global administratör för att få åtkomst till dessa resurser.

När du höjer din åtkomst tilldelas du rollen [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator) `/`i Azure i rotomfång ( ).På så sätt kan du visa alla resurser och tilldela åtkomst i en prenumeration eller hanteringsgrupp i katalogen. Användaråtkomstadministratörsrolltilldelningar kan tas bort med Azure PowerShell, Azure CLI eller REST API.

Du bör ta bort den här förhöjda åtkomsten när du har gjort de ändringar du behöver göra i rotomfånget.

![Upphöj åtkomst](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

### <a name="elevate-access-for-a-global-administrator"></a>Öka åtkomsten för en global administratör

Följ dessa steg för att öka åtkomsten för en global administratör med hjälp av Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com) eller [Azure Active Directory-administrationscentret](https://aad.portal.azure.com) som global administratör.

1. Sök efter och välj **Azure Active Directory**.

   ![Välj Azure Active Directory - skärmdump](./media/elevate-access-global-admin/search-for-azure-active-directory.png)

1. Välj **Egenskaper**under **Hantera**.

   ![Välj egenskaper för Azure Active Directory-egenskaper – skärmbild](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Under **Åtkomsthantering för Azure-resurser**anger du växlingsknappen till **Ja**.

   ![Åtkomsthantering för Azure-resurser – skärmbild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du anger växlingsknappen till **Ja**tilldelas du rollen Administratör för användaråtkomst i Azure RBAC vid rotomfattning (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Den här växlingsknappen är endast tillgänglig för användare som har tilldelats rollen Global administratör i Azure AD.

   När du anger växlingsknappen till **Nej**tas rollen Administratör för användaråtkomst i Azure RBAC bort från ditt användarkonto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanteringsgrupper som du har beviljats åtkomst till.

    > [!NOTE]
    > Om du använder [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)ändras inte den här växlingsknappen till **Nej**om du inaktiverar rolltilldelningen . Om du vill behålla minsta möjliga behörighet rekommenderar vi att du ställer in den här växlingsknappen till **Nej** innan du inaktiverar rolltilldelningen.
    
1. Klicka på **Spara** om du vill spara inställningen.

   Den här inställningen är inte en global egenskap och gäller endast för den inloggade användaren. Du kan inte höja åtkomsten för alla medlemmar i rollen Global administratör.

1. Logga ut och logga in igen för att uppdatera din åtkomst.

    Du bör nu ha tillgång till alla prenumerationer och hanteringsgrupper i katalogen. När du visar iam-fönstret (Access Control) kommer du att märka att du har tilldelats rollen Administratör för användaråtkomst i rotomfattning.

   ![Prenumerationsrolltilldelningar med rotomfattning - skärmdump](./media/elevate-access-global-admin/iam-root.png)

1. Gör de ändringar du behöver göra vid den förhöjda åtkomsten.

    Information om hur du tilldelar roller finns i [Hantera åtkomst med RBAC och Azure-portalen](role-assignments-portal.md). Om du använder PIM (Azure AD Privileged Identity Management) läser [du Identifiera Azure-resurser för att hantera i PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) eller [Tilldela Azure-resursroller i PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

### <a name="remove-elevated-access"></a>Ta bort förhöjd åtkomst

Så här tar du bort rolltilldelningen för användaråtkomstadministratör vid rotomfattning (`/`) gör du så här.

1. Logga in som samma användare som användes för att öka åtkomsten.

1. Klicka på Azure **Active Directory** i navigeringslistan och klicka sedan på **Egenskaper**.

1. Ange **åtkomsthantering för Azure-resurser** växla tillbaka till **Nr**. Eftersom det här är en inställning per användare måste du vara inloggad som samma användare som användes för att öka åtkomsten.

    Om du försöker ta bort rolltilldelningen för användaråtkomstadministratörer i fönstret Åtkomstkontroll (IAM) visas följande meddelande. Om du vill ta bort rolltilldelningen måste du ange växlingsknappen tillbaka till **Nej** eller använda Azure PowerShell, Azure CLI eller REST API.

    ![Ta bort rolltilldelningar med rotomfång](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Lista rolltilldelning vid rotomfattning (/)

Om du vill visa rolltilldelningen för`/`användaråtkomstadministratör för en användare i rotomfattning ( ) använder du kommandot [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

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

### <a name="remove-elevated-access"></a>Ta bort förhöjd åtkomst

Så här tar du bort rolltilldelningen för`/`användaråtkomstadministratör för dig själv eller en annan användare i rotomfattning ( ) gör du så här.

1. Logga in som en användare som kan ta bort förhöjd åtkomst. Detta kan vara samma användare som användes för att öka åtkomsten eller en annan global administratör med förhöjd åtkomst vid rotomfång.

1. Använd kommandot [Ta bort AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) för att ta bort rolltilldelningen för administratör för användaråtkomst.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-role-assignment-at-root-scope-"></a>Lista rolltilldelning vid rotomfattning (/)

Om du vill visa rolltilldelningen för`/`användaråtkomstadministratör för en användare i rotomfattning ( ) använder du kommandot [az role assignment list.](/cli/azure/role/assignment#az-role-assignment-list)

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

### <a name="remove-elevated-access"></a>Ta bort förhöjd åtkomst

Så här tar du bort rolltilldelningen för`/`användaråtkomstadministratör för dig själv eller en annan användare i rotomfattning ( ) gör du så här.

1. Logga in som en användare som kan ta bort förhöjd åtkomst. Detta kan vara samma användare som användes för att öka åtkomsten eller en annan global administratör med förhöjd åtkomst vid rotomfång.

1. Använd kommandot [ta bort az-rolltilldelning](/cli/azure/role/assignment#az-role-assignment-delete) för att ta bort rolltilldelningen för administratör för användaråtkomst.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>Öka åtkomsten för en global administratör

Använd följande grundläggande steg för att öka åtkomsten för en global administratör med REST API.

1. Använda REST, `elevateAccess`anrop , vilket ger dig rollen`/`Administratör för användaråtkomst i rotomfattning ( ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Skapa en [rolltilldelning](/rest/api/authorization/roleassignments) för att tilldela en roll i alla scope. I följande exempel visas egenskaperna för tilldelning av rollen {roleDefinitionID} vid rotomfattning (`/`):

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

1. När du är administratör för användaråtkomst kan du`/`även ta bort rolltilldelningar i rotomfattning ( ).

1. Ta bort administratörsbehörigheten för användaråtkomst tills de behövs igen.

### <a name="list-role-assignments-at-root-scope-"></a>Lista rolltilldelningar i rotomfattning (/)

Du kan lista alla rolltilldelningar för en`/`användare i rotomfattning ( ).

- Anropa [GET-rollTilldelningar](/rest/api/authorization/roleassignments/listforscope) där `{objectIdOfUser}` är objekt-ID för den användare vars rolltilldelningar du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lista neka tilldelningar i rotomfattning (/)

Du kan lista alla neka-tilldelningar för en`/`användare i rotomfattning ( ).

- Anropa GET-nekaNtilldelningar `{objectIdOfUser}` där är objekt-ID för den användare vars nekande tilldelningar som du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Ta bort förhöjd åtkomst

När du `elevateAccess`anropar skapar du en rolltilldelning åt dig själv, så för att återkalla dessa privilegier måste du ta bort rolltilldelningen för användaråtkomstadministratör för dig själv i rotomfattningen (`/`)

1. Anropa [GET-rollDefinitioner](/rest/api/authorization/roledefinitions/get) där `roleName` är lika med användaråtkomstadministratör för att fastställa namn-ID för rollen Administratör för användaråtkomst.

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

    Spara ID:et från parametern `name` i det här fallet `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

1. Du måste också ange rolltilldelningen för katalogadministratören i katalogomfattningen. Lista alla tilldelningar i `principalId` katalogomfånget för katalogadministratören som gjorde det upphöjda åtkomstanropet. Då visas alla tilldelningar i katalogen för objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >En katalogadministratör bör inte ha många tilldelningar, om den föregående frågan returnerar för många tilldelningar, kan du också fråga efter alla tilldelningar bara på katalogomfattningsnivå och sedan filtrera resultaten:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. De tidigare anropen returnerar en lista över rolltilldelningar. Hitta rolltilldelningen där `"/"` scopet finns och `roleDefinitionId` slutar med rollnamns-ID som du hittade i steg 1 och `principalId` matchar katalogadministratörens objectId. 
    
    Exempel på rolltilldelning:
    
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
    
    Återigen, spara ID `name` från parametern, i detta fall 1111111-1111-1111-1111-11111111111111111.

1. Slutligen använder du rolltilldelnings-ID:et för att ta bort tilldelningen som lagts till av: `elevateAccess`

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå de olika rollerna i Azure](rbac-and-directory-admin-roles.md)
- [Hantera åtkomst till Azure-resurser med RBAC och REST API](role-assignments-rest.md)
