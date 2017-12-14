---
title: "Innehavaradministration höjer åtkomst - Azure AD | Microsoft Docs"
description: "Det här avsnittet beskriver den inbyggda i roller för rollbaserad åtkomstkontroll (RBAC)."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: andredm
ms.openlocfilehash: 894ccd13684a79590b75821514ef6922abb8fdaf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Utöka behörighet som en klientadministratör med rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll hjälper innehavaradministratörer hämta tillfälliga rikta åtkomst så att de kan ge högre behörigheter än normalt. En klientadministratör kan höja själv till rollen Administratör för användaråtkomst vid behov. Rollen ger klienten administratörsbehörigheter för att ge sig själv eller andra roller definitionsområdet ”/”.

Den här funktionen är viktigt eftersom det tillåter innehavaradministration att se alla prenumerationer som finns i en organisation. Det gör också för automation-appar som fakturering och granskning för att få åtkomst till alla prenumerationer och ange en korrekt bild av tillståndet för organisationen för fakturerings- eller tillgångshantering.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Använda elevateAccess för klient-åtkomst med Azure AD Administrationscenter

1. Gå till den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) och logga in med du autentiseringsuppgifter.

2. Välj **egenskaper** kvar menyn från Azure AD.

3. I den **egenskaper** bladet hitta **Global administratör kan hantera Azure-prenumerationer**, Välj **Ja**, sedan **spara**.
    > [!IMPORTANT] 
    > När du väljer **Ja**, tilldelar den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användaren som du är inloggad i portalen. **Detta gör att användaren kan se alla andra Azure-prenumerationer.**
    
    > [!NOTE] 
    > När du väljer **nr**, tar bort den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användaren som du är inloggad i portalen.

> [!TIP] 
> Intryck är att det här är en Global egenskap för Azure Active Directory, men den fungerar på grundval av per användare för den inloggade användaren. När du har behörighet som Global administratör i Azure Active Directory, kan du anropa funktionen elevateAccess för användaren som du för närvarande är inloggad i Azure Active Directory Administrationscenter.

![Azure AD Admin Center - egenskaper – Globaladmin kan hantera Azure-prenumeration – skärmbild](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Visa rolltilldelningar definitionsområdet ”/” med hjälp av PowerShell
Visa den **administratör för användaråtkomst** tilldelning vid den  **/**  omfång, använder den `Get-AzureRmRoleAssignment` PowerShell-cmdlet.
    
```
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Exempel på utdata**:

RoleAssignmentId: /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Omfång: /    
Visningsnamn: användarnamn    
SignInName:username@somedomain.com    
RoleDefinitionName: Administratör för användaråtkomst    
RoleDefinitionId: 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
Objekt-ID: d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType: användare    

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Ta bort rolltilldelning i ”/” scope med hjälp av Powershell:
Du kan ta bort tilldelningen med följande PowerShell-cmdlet:
```
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Använd elevateAccess för att ge innehavare åtkomst med REST API

Den grundläggande processen fungerar med följande steg:

1. Använda REST kan anropa *elevateAccess*, vilket ger rollen Administratör för användaråtkomst på ”/” omfång.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Skapa en [rolltilldelning](/rest/api/authorization/roleassignments) att tilldela någon roll på ett scope. I följande exempel visar egenskaperna för att tilldela rollen Läsare på ”/” scope:

    ```
    { "properties":{
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

1.  Anropa GET rolldefinitioner där roleName = administratör för användaråtkomst för att fastställa namnet GUID för rollen Administratör för användaråtkomst.
    1.  Hämta *https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$ filter = roleName + eq +'+ + administratör för användaråtkomst*

        ```
        {"value":[{"properties":{
        "roleName":"User Access Administrator",
        "type":"BuiltInRole",
        "description":"Lets you manage user access to Azure resources.",
        "assignableScopes":["/"],
        "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
        "createdOn":"0001-01-01T08:00:00.0000000Z",
        "updatedOn":"2016-05-31T23:14:04.6964687Z",
        "createdBy":null,
        "updatedBy":null},
        "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "type":"Microsoft.Authorization/roleDefinitions",
        "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
        "nextLink":null}
        ```

        Spara GUID från den *namn* parameter i det här fallet **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Du måste också att lista rolltilldelning för innehavaradministration på klientorganisationsområde. Lista över alla tilldelningar i klient omfånget för PrincipalId av TenantAdmin som gjorde utöka åtkomst anropa. Detta visar en lista över alla tilldelningar i klienten för objekt-ID. 
    1. Hämta *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$ filter = principalId + eq + {objectid}*
    
        >[!NOTE] 
        >En klientadministratör ska inte ha många tilldelningar om frågan ovan returnerar för många tilldelningar, du kan också fråga för alla tilldelningar bara på omfångsnivå för klient och filtrera resultatet: hämta *https://management.azure.com/providers/ Microsoft.Authorization/roleAssignments? api-version 2015-07-01 = & $filter=atScope()*
        
    2. Ovanstående anrop returnera en lista över rolltilldelningar. Hitta rolltilldelningen där omfånget är ”/” och RoleDefinitionId slutar med rollnamnet GUID som du hittade i steg 1 och PrincipalId matchar ObjectId för Tenant Admin. Rolltilldelningen ser ut så här:

        ```
        {"value":[{"properties":{
        "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "principalId":"{objectID}",
        "scope":"/",
        "createdOn":"2016-08-17T19:21:16.3422480Z",
        "updatedOn":"2016-08-17T19:21:16.3422480Z",
        "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
        "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
        "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
        "type":"Microsoft.Authorization/roleAssignments",
        "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
        "nextLink":null}
        ```
        
        Igen och spara GUID från den *namn* parameter i det här fallet **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Använd slutligen den markerade **RoleAssignment ID** att ta bort tilldelningen som lagts till av höjer åtkomst:

        Ta bort https://management.azure.com /providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Hantera rollbaserad åtkomstkontroll med REST](role-based-access-control-manage-access-rest.md)

- [Hantera åtkomst tilldelningar](role-based-access-control-manage-assignments.md) i Azure-portalen
