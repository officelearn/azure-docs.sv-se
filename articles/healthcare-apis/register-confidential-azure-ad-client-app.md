---
title: Registrera ett konfidentiellt klientprogram i Azure Active Directory - Azure-API för FHIR
description: Den här artikeln beskrivs hur du registrerar ett konfidentiellt klientprogram i Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 064cf4bb0b6e9454a4049cc6c32b51b09e5b2a53
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824130"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrera en konfidentiell klient-program i Azure Active Directory

I den här artikeln lär du dig att registrera en konfidentiell klient-program i Azure Active Directory. En klient programregistrering är en Azure Active Directory-representation av ett program som kan användas för att autentiseras för en användare och begär åtkomst till [resursprogram](register-resource-azure-ad-client-app.md). Ett konfidentiellt klientprogram är ett program som kan användas för att lagra en hemlighet och presentera den hemligheten när du begär åtkomsttoken. Exempel på konfidentiell program är serverbaserade program.

Följ stegen nedan för att registrera ett nytt konfidentiell program i portalen.

## <a name="app-registrations-in-azure-portal"></a>App-registreringar i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**.

2. I den **Azure Active Directory** bladet klickar du på **appregistreringar (förhandsversion)**:

    ![Azure-portalen. Ny Appregistrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **ny registrering**.

## <a name="register-a-new-application"></a>Registrera ett nytt program

1. Ge programmet ett namn.

2. Ange en svars-URL. Dessa uppgifter kan ändras senare, men om du vet svars-URL för ditt program, anger du det nu.

    ![Ny Appregistrering för konfidentiell klient.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-behörigheter

Därefter lägger du till API-behörigheter:

1. Öppna den **API-behörigheter** och välj din [FHIR API-resursen programregistrering](register-resource-azure-ad-client-app.md):

    ![Konfidentiell klient. API-behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klicka på **lägga till en behörighet**

3. Välj din API-resurs:

    ![Konfidentiell klient. Mina API:er](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Välj scope (behörigheter) som konfidentiellt programmet ska kunna begära för en användares räkning:

    ![Konfidentiell klient. Delegerade behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Programhemlighet

1. Skapa en programhemlighet (klienthemlighet):

    ![Konfidentiell klient. Programhemlighet](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Ange en beskrivning och varaktighet för hemlighet.

3. När genereras, ska det visas i portalen bara en gång. Anteckna den och lagra den på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar en konfidentiell klient-program i Azure Active Directory. Distribuera ett FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-oss-powershell-quickstart.md)