---
title: Registrera ett resursprogram i Azure Active Directory - Azure-API för FHIR
description: Den här artikeln beskrivs hur du registrerar ett resursprogram i Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e8305c5a69fa3fda29f4f1292b7faa59f8ec3608
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870154"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Registrera ett resursprogram i Azure Active Directory

I den här artikeln du lär dig hur du registrerar en resurs (eller API: et) program i Azure Active Directory. Resursprogram är en Azure Active Directory-representation av FHIR servern själva API: T och klientprogram kan begära åtkomst till resursen vid autentisering. Resursprogrammet kallas även den *målgrupp* i OAuth kallas.

## <a name="app-registrations-in-azure-portal"></a>App-registreringar i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**.

2. I den **Azure Active Directory** bladet klickar du på **appregistreringar (förhandsversion)**:

    ![Azure-portalen. Ny Appregistrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **ny registrering**.

## <a name="add-a-new-application-registration"></a>Lägg till en ny programregistrering

Fyll i informationen för det nya programmet. Det finns inga särskilda krav för visningsnamnet, men ange värdet till URI: N för FHIR-server gör det enkelt att hitta:

![Ny programregistrering](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>Ange identifierar-URI och definiera omfång

Resursprogram har en identifierar-URI (program-ID-URI), vilka klienter kan använda när du begär åtkomst till resursen. Det här värdet fylls den `aud` anspråk för åtkomsttoken. Vi rekommenderar att du ställer in den här URI: N ska vara URI för FHIR-server. För SMART FHIR Apps det förutsätts att den *målgrupp* är URI: N för FHIR-server.

1. Klicka på **exponerar ett API**

2. Klicka på **ange** bredvid *program-ID-URI*.

3. Ange identifierar-URI och klicka på **spara**. En bra identifier är URI: N för FHIR-server.

4. Klicka på **lägga till ett scope** och Lägg till alla omfattningar som du vill definiera du API: et. Azure AD tillåter för närvarande inte snedstreck (`/`) i scopenamn. Vi rekommenderar att du använder `$` i stället. Ett omfång som `patient/*.read` skulle vara `patient$*.read`.

    ![Publik och omfång](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>Definiera roller för programmet

Azure API för FHIR och OSS FHIR-Server för Azure-användning [Azure Active Directory programroller](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) för rollbaserad åtkomstkontroll. För att definiera vilka roller ska vara tillgängliga för ditt FHIR Server API, öppna resursprogrammet [manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/):

1. Klicka på **Manifest**:

    ![Programroller](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. I den `appRoles` egenskapen, Lägg till roller som du vill att användare eller program ha:

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

I den här artikeln har du lärt dig hur du registrerar en resursprogram i Azure Active Directory. Distribuera ett FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-oss-powershell-quickstart.md)