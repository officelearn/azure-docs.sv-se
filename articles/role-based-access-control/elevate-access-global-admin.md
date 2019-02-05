---
title: Utöka behörighet för att hantera alla Azure-prenumerationer och hanteringsgrupper | Microsoft Docs
description: Beskriver hur du höjer åtkomst för en Global administratör för att hantera alla prenumerationer och hanteringsgrupper i Azure Active Directory med Azure portal eller REST API.
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
ms.openlocfilehash: d6778e1749493a04a73d0ac210c1557b89343d00
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695588"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Utöka behörighet för att hantera alla Azure-prenumerationer och hanteringsgrupper

Som Global administratör i Azure Active Directory (AD Azure), kanske du inte har åtkomst till alla prenumerationer och hanteringsgrupper i din katalog. Den här artikeln beskriver hur kan du höja din åtkomst till alla prenumerationer och hanteringsgrupper.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Varför skulle du behöva öka din till?

Om du är en Global administratör kan finnas det tillfällen när du vill göra följande:

- Få åtkomst till en Azure-prenumerations- eller grupp när en användare har förlorat åtkomst
- Bevilja en annan användare eller dig själv åtkomst till en Azure-prenumerations- eller grupp
- Se alla Azure-prenumerationer eller hanteringsgrupper i en organisation
- Ge en automation-app (till exempel en app för fakturering eller granskning) åtkomst till alla Azure-prenumerationer eller hanteringsgrupper

## <a name="how-does-elevate-access-work"></a>Hur höjer åtkomst till arbete?

Azure AD och Azure-resurser är skyddade oberoende från varandra. Det vill säga Azure AD-rolltilldelningar beviljas inte åtkomst till Azure-resurser och Azure rolltilldelningar bevilja inte åtkomst till Azure AD. Men om du är en [Global administratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) i Azure AD, du kan tilldela dig själv åtkomst till alla Azure-prenumerationer och hanteringsgrupper i din katalog. Använd den här funktionen om du inte har åtkomst till resurser i Azure-prenumeration, till exempel virtuella datorer eller lagringskonton, och du vill använda din Global administratörsbehörighet för att få åtkomst till resurserna.

När du utöka din behörighet du kommer att tilldelas den [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) roll i Azure på rotscopet (`/`). På så sätt kan du visa alla resurser och tilldela åtkomst i någon prenumeration eller hanteringsgrupp i katalogen. Administratör för användaråtkomst rolltilldelningar kan tas bort med hjälp av PowerShell.

Du bör ta bort den här utökad åtkomst när du har gjort ändringar som du behöver göra vid rotscopet.

![Utöka behörighet](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure Portal

Följ stegen nedan för att utöka behörighet för Global administratör med hjälp av Azure portal.

1. Logga in på den [Azure-portalen](https://portal.azure.com) eller [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) som Global administratör.

1. I listan över navigeringsfönstret klickar du på **Azure Active Directory** och klicka sedan på **egenskaper**.

   ![Azure AD-egenskaper – skärmbild](./media/elevate-access-global-admin/aad-properties.png)

1. Under **åtkomsthantering för Azure-resurser**, Ställ in växlaren **Ja**.

   ![Åtkomsthantering för Azure-resurser – skärmbild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du anger växlingsknappen till **Ja**, tilldelas du rollen Administratör för användaråtkomst i Azure RBAC vid rotscopet (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalog. Den här växlingsknappen är endast tillgänglig för användare som har tilldelats rollen Global administratör i Azure AD.

   När du anger växlingsknappen till **nr**, rollen Administratör för användaråtkomst i Azure RBAC tas bort från ditt konto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalog. Du kan visa och hantera den Azure-prenumerationer och hanteringsgrupper som du har beviljats åtkomst.

1. Klicka på **spara** att spara dina inställningar.

   Den här inställningen gäller enbart för den inloggade användaren är inte en global egenskap. Du kan inte utöka behörighet för alla medlemmar i rollen som Global administratör.

1. Logga ut och logga in för att uppdatera din åtkomst.

    Du bör nu ha åtkomst till alla prenumerationer och hanteringsgrupper i din katalog. Lägg märke till att du har tilldelats rollen Administratör för användaråtkomst vid rotscopet.

   ![Prenumeration rolltilldelningar med rotscopet – skärmbild](./media/elevate-access-global-admin/iam-root.png)

1. Göra de ändringar som du behöver göra på den utökade behörigheten.

    Information om hur du tilldelar roller finns i [hantera åtkomst med RBAC och Azure portal](role-assignments-portal.md). Om du använder Azure AD Privileged Identity Management (PIM), se [identifiera Azure-resurser för att hantera i PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) eller [tilldela Azure-resursroller i PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. När du är klar kan du ange den **åtkomsthantering för Azure-resurser** växla tillbaka till **nr**. Eftersom detta är en inställning för per användare, måste du vara inloggad som samma användare som har använts för att utöka behörighet.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Lista rolltilldelning vid rotscopet (/)

Visa en lista över administratör för användaråtkomst rolltilldelning för en användare vid rotscopet (`/`), använder den [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) kommando.

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

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Ta bort en rolltilldelning vid rotscopet (/)

Ta bort en administratör för användaråtkomst rolltilldelning för en användare vid rotscopet (`/`), Följ stegen nedan.

1. Logga in som en användare som kan ta bort utökad åtkomst. Detta kan vara samma användare som används för att utöka åtkomst eller en annan Global administratör med utökad åtkomst vid rotscopet.


1. Använd den [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) kommando för att ta bort rolltilldelningen administratör för användaråtkomst.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST-API

### <a name="elevate-access-for-a-global-administrator"></a>Utöka behörighet för Global administratör

Använd följande grundläggande steg för att utöka behörighet för Global administratör med hjälp av REST-API.

1. Med hjälp av REST, anropa `elevateAccess`, vilket ger dig rollen Administratör för användaråtkomst vid rotscopet (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Skapa en [rolltilldelning](/rest/api/authorization/roleassignments) att tilldela en roll i alla omfånget. I följande exempel visas egenskaperna för att tilldela rollen {roleDefinitionID} vid rotscopet (`/`):

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

1. När en administratör för användaråtkomst, du kan också ta bort rolltilldelningar vid rotscopet (`/`).

1. Ta bort din administratör för användaråtkomst privilegier förrän de behövs igen.

### <a name="list-role-assignments-at-the-root-scope-"></a>Lista rolltilldelningar vid rotscopet (/)

Du kan lista alla rolltilldelningar för en användare vid rotscopet (`/`).

- Anropa [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) där `{objectIdOfUser}` är objekt-ID för användaren vars rolltilldelningar som du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Lista neka tilldelningar vid rotscopet (/)

Du kan visa en lista över neka tilldelningar för en användare vid rotscopet (`/`).

- Anropa GET denyAssignments där `{objectIdOfUser}` är objekt-ID för användaren vars neka tilldelningar som du vill hämta.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst

När du anropar `elevateAccess`, som du skapar en rolltilldelning själv, så för att återkalla de behörigheterna som du behöver ta bort tilldelningen.

1. Anropa [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) där `roleName` är lika med administratör för användaråtkomst för att fastställa namn-ID för rollen Administratör för användaråtkomst.

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

    Spara ID: T från den `name` parametern i det här fallet `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Du måste också visa en lista över rolltilldelningen för directory-administratör i directory omfånget. Lista över alla tilldelningar i directory omfånget för den `principalId` till directory-administratören som gjorts utöka åtkomsten anropa. Detta visar en lista över alla tilldelningar i katalogen för objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >En katalogadministratör ska inte ha många tilldelningar, om den föregående frågan returnerar för många tilldelningar, du kan också fråga för alla tilldelningar just på nivån för katalogen omfång och sedan filtrera resultaten: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Tidigare anrop returnerar en lista över rolltilldelningar. Hitta rolltilldelningen där omfånget är `"/"` och `roleDefinitionId` slutar med rollen namn-ID som du identifierade i steg 1 och `principalId` matchar objectId för directory-administratör. 
    
    Exemplet rolltilldelning:

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
        
    Igen, spara ID: T från den `name` parametern i det här fallet e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Använd slutligen rollen tilldelnings-ID för att ta bort tilldelningen har lagts till av `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå de olika rollerna](rbac-and-directory-admin-roles.md)
- [Rollbaserad åtkomstkontroll med REST](role-assignments-rest.md)
