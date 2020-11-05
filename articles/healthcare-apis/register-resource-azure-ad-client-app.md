---
title: Registrera en resurs-app i Azure AD – Azure API för FHIR
description: Registrera en resurs-eller API-app i Azure Active Directory så att klient program kan begära åtkomst till resursen vid autentisering.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e22eaacd73bb15ddf43f416831ff5ff42923b6e0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393395"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrera ett resurs program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett resurs-eller API-program i Azure Active Directory. Ett resurs program är en Azure Active Directory representation av själva FHIR-serverns API och klient program kan begära åtkomst till resursen vid autentisering. Resurs programmet kallas även för *mål gruppen* i OAuth-parlance.

## <a name="azure-api-for-fhir"></a>Azure API för FHIR

Om du använder Azure-API: et för FHIR skapas ett resurs program automatiskt när du distribuerar tjänsten. Så länge du använder Azure-API: et för FHIR i samma Azure Active Directory klient organisation som du distribuerar ditt program, kan du hoppa över den här instruktions guiden och distribuera ditt Azure-API för FHIR för att komma igång.

Om du använder en annan Azure Active Directory klient (som inte är associerad med din prenumeration) kan du importera Azure-API: et för FHIR resurs program till din klient med PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

eller så kan du använda Azure CLI:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR Server för Azure

Om du använder FHIR-servern med öppen källkod för Azure följer du stegen nedan för att registrera ett resurs program.

### <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på [Azure Portal](https://portal.azure.com).

2. I bladet **Azure Active Directory** klickar du på **Appregistreringar** :

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **nya registreringen**.

### <a name="add-a-new-application-registration"></a>Lägg till en ny program registrering

Fyll i informationen för det nya programmet. Det finns inga särskilda krav för visnings namnet, men om du anger den till URI: n för FHIR-servern är det enkelt att hitta:

![Ny programregistrering](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Ange Identifier-URI och definiera omfång

Ett resurs program har en identifierare-URI (program-ID-URI) som klienter kan använda för att begära åtkomst till resursen. Det här värdet fyller i `aud` anspråket för åtkomsttoken. Vi rekommenderar att du ställer in den här URI: n på din FHIR-Server. För SMART on FHIR-appar förutsätts att *mål gruppen* är URI: n för FHIR-servern.

1. Klicka på **exponera ett API**

2. Klicka på **Ange** bredvid *program-ID-URI*.

3. Ange ID för URI: n och klicka på **Spara**. En korrekt ID-URI är URI: n för din FHIR-Server.

4. Klicka på **Lägg till ett omfång** och Lägg till eventuella omfång som du vill definiera för ditt API. Du måste lägga till minst ett omfång för att kunna bevilja behörigheter till resurs programmet i framtiden. Om du inte har några speciella omfattningar som du vill lägga till kan du lägga till user_impersonation som ett omfång.

![Mål grupp och omfattning](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Definiera program roller

Azure API för FHIR och OSS FHIR-servern för Azure använder [Azure Active Directory program roller](/azure/architecture/multitenant-identity/app-roles) för rollbaserad åtkomst kontroll. Om du vill definiera vilka roller som ska vara tillgängliga för FHIR-serverns API öppnar du resurs programmets [manifest](/azure/active-directory/active-directory-application-manifest/):

1. Klicka på **manifest** :

    ![Program roller](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. I `appRoles` egenskapen lägger du till de roller som du vill att användare eller program ska ha:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett resurs program i Azure Active Directory. Registrera sedan ditt konfidentiella klient program.
 
>[!div class="nextstepaction"]
>[Registrera konfidentiellt klient program](register-confidential-azure-ad-client-app.md)