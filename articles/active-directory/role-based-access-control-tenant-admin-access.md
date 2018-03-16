---
title: "Innehavaradministration höjer åtkomst - Azure AD | Microsoft Docs"
description: "Det här avsnittet beskriver den inbyggda i roller för rollbaserad åtkomstkontroll (RBAC)."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Utöka behörighet som en klientadministratör med rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll hjälper innehavaradministratörer hämta tillfälliga rikta åtkomst så att de kan ge högre behörigheter än normalt. Innehavaradministratörer kan höja sig själva till rollen Administratör för användaråtkomst vid behov. Rollen ger innehavaradministratörer behörigheter att ge sig själva eller andra roller definitionsområdet ”/”.

Den här funktionen är viktigt eftersom det tillåter innehavaradministration att se alla prenumerationer som finns i en organisation. Det gör också för automation-appar som fakturering och granskning för att få åtkomst till alla prenumerationer och ange en korrekt bild av tillståndet för organisationen för fakturerings- eller tillgångshantering.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Använda elevateAccess för klient-åtkomst med Azure AD Administrationscenter

1. Gå till den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) och logga in med dina autentiseringsuppgifter.

2. Välj **egenskaper** kvar menyn från Azure AD.

3. Hitta **Global administratör kan hantera Azure-prenumerationer**, Välj **Ja**, sedan **spara**.
    > [!IMPORTANT] 
    > När du väljer **Ja**, tilldelar den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användaren som du är inloggad i portalen. **Detta gör att användaren kan se alla andra Azure-prenumerationer.**
    
    > [!NOTE] 
    > När du väljer **nr**, tar bort den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användaren som du är inloggad i portalen.

> [!TIP] 
> Intryck är att det här är en Global egenskap för Azure Active Directory, men den fungerar på grundval av per användare för den inloggade användaren. När du har behörighet som Global administratör i Azure Active Directory, kan du anropa funktionen elevateAccess för användaren att du är inloggad i Azure Active Directory Administrationscenter.

![Azure AD Admin Center - egenskaper – Global administratör kan hantera Azure-prenumeration – skärmbild](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Visa rolltilldelningar definitionsområdet ”/” med hjälp av PowerShell
Visa den **administratör för användaråtkomst** tilldelning vid den  **/**  omfång, använder den `Get-AzureRmRoleAssignment` PowerShell-cmdlet.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Exempel på utdata**:
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Ta bort rolltilldelning i ”/” scope med hjälp av Powershell:
Du kan ta bort tilldelningen med följande PowerShell-cmdlet:

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Använd elevateAccess för att ge innehavare åtkomst med REST API

Den grundläggande processen fungerar med följande steg:

1. Använda REST kan anropa *elevateAccess*, vilket ger rollen Administratör för användaråtkomst på ”/” omfång.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Skapa en [rolltilldelning](/rest/api/authorization/roleassignments) att tilldela någon roll på ett scope. I följande exempel visar egenskaperna för att tilldela rollen Läsare på ”/” scope:

    ```json
    { 
      "properties": {
        "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
        "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
        "scope": "/"
      },
      "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. När en användare åtkomst till Admin, kan du även ta bort rolltilldelningar på ”/” omfång.

4. Återkalla användare åtkomst till Admin-privilegier tills de behövs igen.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Hur du ångrar elevateAccess åtgärd med REST API

När du anropar *elevateAccess* du skapa en rolltilldelning för dig själv, så för att återkalla behörigheter måste du ta bort tilldelningen.

1.  Anropa GET roleDefinitions där roleName = administratör för användaråtkomst för att fastställa namnet GUID för rollen Administratör för användaråtkomst.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
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

    Spara GUID från den *namn* parameter i det här fallet **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Du måste också att lista rolltilldelning för innehavaradministration på klientorganisationsområde. Lista över alla tilldelningar i klient omfånget för PrincipalId av TenantAdmin som gjorde utöka åtkomst anropa. Detta visar en lista över alla tilldelningar i klienten för objekt-ID.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >En klientadministratör ska inte ha många tilldelningar, om den föregående frågan returnerar för många tilldelningar, du kan också fråga för alla tilldelningar bara på omfångsnivå för klient och filtrera resultatet: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Tidigare anrop returnera en lista över rolltilldelningar. Hitta rolltilldelningen där omfånget är ”/” och RoleDefinitionId slutar med rollnamnet GUID som du hittade i steg 1 och PrincipalId matchar ObjectId för Tenant Admin. 
    
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
        
    Igen och spara GUID från den *namn* parameter i det här fallet **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Använd slutligen den markerade **RoleAssignment ID** att ta bort tilldelningen som lagts till av höjer åtkomst:

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Hantera rollbaserad åtkomstkontroll med REST](role-based-access-control-manage-access-rest.md)

- [Hantera åtkomst tilldelningar](role-based-access-control-manage-assignments.md) i Azure-portalen
