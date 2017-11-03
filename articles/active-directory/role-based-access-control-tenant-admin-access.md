---
title: "Innehavaradministration höjer åtkomst - Azure AD | Microsoft Docs"
description: "Det här avsnittet beskriver den inbyggda i roller för rollbaserad åtkomstkontroll (RBAC)."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andredm
ms.openlocfilehash: 22b62be1773c5042ecf6ee078e68a4ffdf791d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Utöka behörighet som en klientadministratör med rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll hjälper innehavaradministratörer hämta tillfälliga rikta åtkomst så att de kan ge högre behörigheter än normalt. En klientadministratör kan höja själv till rollen Administratör för användaråtkomst vid behov. Rollen ger klienten administratörsbehörigheter för att ge sig själv eller andra roller definitionsområdet ”/”.

Den här funktionen är viktigt eftersom det tillåter innehavaradministration att se alla prenumerationer som finns i en organisation. Det gör även för automation-appar (som fakturering och granskning) att komma åt alla prenumerationer och ange en korrekt bild av tillståndet för organisationen för hantering av fakturerings- eller tillgången.  

## <a name="how-to-use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Hur du använder elevateAccess för klient-åtkomst med Azure AD Administrationscenter

I den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) du kan anropa funktionen från **egenskaper**.
Funktionen kallas **Global administratör kan hantera Azure-prenumerationer**. Intryck är att det här är en Global egenskap för Azure Active Directory, men den fungerar på grundval av per användare för den inloggade användaren. När du har behörighet som Global administratör i Azure Active Directory, kan du anropa funktionen elevateAccess för användaren som du för närvarande är inloggad i Azure Active Directory Administrationscenter.

Att välja **Ja** och sedan **spara**: detta **tilldelar** den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användare med som du är inloggad på portalen.

Att välja **nr** och sedan **spara**: detta **tar bort** den **administratör för användaråtkomst** roll i roten ”/” (Rotscopet) för användare med som du är inloggad på portalen.

![Azure AD Admin Center - egenskaper – Globaladmin kan hantera Azure-prenumeration – skärmbild](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="how-to-use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Hur du använder elevateAccess för att ge innehavare åtkomst med REST API

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

1.  Anropa [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get) där roleName = administratör för användaråtkomst för att fastställa namnet GUID för rollen Administratör för användaråtkomst. Svaret ska se ut så här:

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

2. Anropa [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get) där principalId = egna ObjectId. Detta visar alla tilldelningar i klienten. Leta efter den där omfånget är ”/” och RoleDefinitionId slutar med rollnamnet GUID som du hittade i steg 1. Rolltilldelningen bör se ut så här:

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

3. Slutligen anropa [bort roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById) där roleAssignmentId = namnet GUID som du hittade i steg 2.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Hantera rollbaserad åtkomstkontroll med REST](role-based-access-control-manage-access-rest.md)

- [Hantera åtkomst tilldelningar](role-based-access-control-manage-assignments.md) i Azure-portalen
