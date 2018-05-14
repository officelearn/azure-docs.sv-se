---
title: Höja åtkomst för en Global administratör i Azure Active Directory | Microsoft Docs
description: Beskriver hur du höja åtkomst för en Global administratör i Azure Active Directory med Azure portal eller REST API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Höja åtkomst för en Global administratör i Azure Active Directory

Om du är en [Global administratör](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) i Azure Active Directory (Azure AD), det kan finnas tillfällen när du vill göra följande:

- Få åtkomst till en Azure-prenumeration när en användare har förlorat åtkomst
- Ge en annan användare eller dig själv åtkomst till en Azure-prenumeration
- Se alla Azure-prenumerationer i en organisation
- Ge en automation-app (till exempel en fakturering eller granskning app) åtkomst till alla Azure-prenumerationer

Som standard styra administratörsroller i Azure AD och Azure rollbaserad åtkomst (RBAC) roller gör inte span Azure AD och Azure. Om du är en Global administratör i Azure AD kan du höja din åtkomst för att hantera Azure-prenumerationer och hanteringsgrupper. När du utöka din behörighet får du den [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) roll (en roll med RBAC) i alla prenumerationer för en viss klient. Rollen Administratör för användaråtkomst gör att du kan ge andra användare åtkomst till Azure-resurser vid rotscopet (`/`).

Höjningen ska vara tillfällig och endast när det behövs.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Höja åtkomst för en Global administratör i Azure Portal

1. Logga in på den [Azure-portalen](https://portal.azure.com) eller [Azure Active Directory Administrationscenter](https://aad.portal.azure.com).

1. Klicka i listan navigering **Azure Active Directory** och klicka sedan på **egenskaper**.

   ![Azure AD-egenskaper – skärmbild](./media/elevate-access-global-admin/aad-properties.png)

1. Under **Global administratör kan hantera Azure-prenumerationer och Hanteringsgrupper**, ange växeln till **Ja**.

   ![Global administratör kan hantera Azure-prenumerationer och hantering av grupper – skärmbild](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   När du anger växeln till **Ja**, ditt globala administratörskonto (för tillfället inloggad användare) har lagts till rollen Administratör för användaråtkomst i Azure RBAC vid rotscopet (`/`), vilket ger dig åtkomst till Visa och rapportera på alla Azure-prenumerationer som är associerade med Azure AD-klienten.

   När du anger växeln till **nr**, ditt globala administratörskonto (för tillfället inloggad användare) tas bort från rollen Administratör för användaråtkomst i Azure RBAC. Du kan inte se alla Azure-prenumerationer som är associerade med Azure AD-klient, och du kan visa och hantera endast Azure prenumerationer som du har beviljats åtkomst.

1. Klicka på **spara** att spara dina inställningar.

   Den här inställningen gäller bara för den inloggade användaren är inte en global egenskap.

1. Utföra uppgifter som du behöver göra på den utökade behörigheten. När du är klar kan du ange växeln tillbaka till **nr**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Lista rolltilldelning definitionsområdet rot (/) med hjälp av PowerShell

Att lista administratör för användaråtkomst rolltilldelning för en användare vid rotscopet (`/`), använder den [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) kommando.

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
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
```

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>Ta bort en rolltilldelning definitionsområdet rot (/) med hjälp av PowerShell

Ta bort en administratör för användaråtkomst rolltilldelning för en användare vid rotscopet (`/`), använder den [ta bort AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) kommando.

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Höja åtkomst för en Global administratör med hjälp av REST-API

Använd följande grundläggande steg för att höja åtkomst för en Global administratör med hjälp av REST-API.

1. Använda REST kan anropa `elevateAccess`, som ger åtkomst till rollen vid rotscopet (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Skapa en [rolltilldelning](/rest/api/authorization/roleassignments) att tilldela någon roll på ett scope. Följande exempel visar egenskaperna för att tilldela rollen {roleDefinitionID} vid rotscopet (`/`):

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

1. Återkalla din administratörsbehörighet användaren åtkomst tills de behövs igen.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Hur du ångrar elevateAccess åtgärd med REST API

När du anropar `elevateAccess`du skapar en rolltilldelning själv, så för att återkalla behörigheter som du behöver ta bort tilldelningen.

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

    Spara-ID från den `name` parameter i det här fallet `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Du måste också att lista rolltilldelning för klientadministratör på klientorganisationsområde. Visa en lista med alla tilldelningar i klient omfånget för den `principalId` av Innehavaradministratör som gjorde utöka åtkomst anropa. Detta visar en lista över alla tilldelningar i klienten för objekt-ID.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >En Innehavaradministratör ska inte ha många tilldelningar, om den föregående frågan returnerar för många tilldelningar, du kan också fråga för alla tilldelningar bara på omfångsnivå för klient och filtrera resultatet: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Tidigare anrop returnera en lista över rolltilldelningar. Hitta rolltilldelningen där omfånget är ”/” och `roleDefinitionId` slutar med rollen namn-ID som du hittade i steg 1 och `principalId` matchar objectId för innehavaradministratören. 
    
    Exempel rolltilldelningen:

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
        
    Igen och spara ID från den `name` parameter i det här fallet e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Använd slutligen tilldelnings-ID för att ta bort tilldelningen som lagts till av `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- [Rollbaserad åtkomstkontroll med REST](role-assignments-rest.md)
- [Hantera åtkomst](role-assignments-users.md)
