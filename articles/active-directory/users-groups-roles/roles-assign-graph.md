---
title: Tilldela Azure AD-administratörsroller med Microsoft Graph API | Microsoft-dokument
description: Tilldela och ta bort Azure AD-administratörsroller med Graph API i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3632f8a360df8837569104232b7380fdc8383953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559155"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Tilldela anpassade administratörsroller med Microsoft Graph API i Azure Active Directory 

Du kan automatisera hur du tilldelar roller till användarkonton med hjälp av Microsoft Graph API. Den här artikeln innehåller post-, GET- och DELETE-åtgärder för rollAssignments.

## <a name="required-permissions"></a>Nödvändiga behörigheter

Anslut till din Azure AD-klient med hjälp av ett globalt administratörskonto eller privilegierad identitetsadministratör för att tilldela eller ta bort roller.

## <a name="post-operations-on-roleassignment"></a>POST-åtgärder för rolltilldelning

HTTP-begäran om att skapa en rolltilldelning mellan en användare och en rolldefinition.

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Innehåll

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Svar

``` HTTP
HTTP/1.1 201 Created
```

HTTP-begäran om att skapa en rolltilldelning där huvud- eller rolldefinitionen inte finns

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Innehåll

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

Svar

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-begäran om att skapa en enskild resursomfattad rolltilldelning på en inbyggd rolldefinition.

> [!NOTE] 
> Inbyggda roller har idag en begränsning där de endast kan begränsas till "/" organisationsomfattande omfattning eller "/AU/*" omfattning. En resurs omfång fungerar inte för inbyggda roller, men fungerar för anpassade roller.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Innehåll

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

Svar

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>FÅ åtgärder på RollAssignment

HTTP-begäran om att få en rolltilldelning för ett visst huvudnamn

HÄMTA

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Svar

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

HTTP-begäran om att få en rolltilldelning för en viss rolldefinition.

HÄMTA

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Svar

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

HTTP-begäran om att få en rolltilldelning efter ID.

HÄMTA

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svar

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>TA BORT åtgärder på rolltilldelning

HTTP-begäran om att ta bort en rolltilldelning mellan en användare och en rolldefinition.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svar
``` HTTP
HTTP/1.1 204 No Content
```

HTTP-begäran om att ta bort en rolltilldelning som inte längre finns

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svar

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP-begäran om att ta bort en rolltilldelning mellan själv- och inbyggd rolldefinition

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Svar

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Känn dig välkommen att dela med oss på [Azure AD administrativa roller forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratörsrolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
