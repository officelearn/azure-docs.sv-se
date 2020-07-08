---
title: Registrera en konfidentiell klient app i Azure AD – Azure API för FHIR
description: Registrera ett konfidentiellt klient program i Azure Active Directory som autentiseras för en användares räkning och begär åtkomst till resurs program.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6642adf5f51be1f4f54c538624829fe9004d3907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871878"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrera ett konfidentiellt klient program i Azure Active Directory

I den här självstudien får du lära dig hur du registrerar ett konfidentiellt klient program i Azure Active Directory. 

En klient program registrering är en Azure Active Directory representation av ett program som kan användas för att autentisera för en användares räkning och begära åtkomst till [resurs program](register-resource-azure-ad-client-app.md). Ett konfidentiellt klient program är ett program som kan vara betrott att lagra en hemlighet och presentera hemligheten när de begär åtkomsttoken. Exempel på konfidentiella program är program på Server sidan.

Följ stegen nedan om du vill registrera ett nytt konfidentiellt program i portalen.

## <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på [Azure Portal](https://portal.azure.com).

2. I bladet **Azure Active Directory** klickar du på **Appregistreringar**:

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **nya registreringen**.

## <a name="register-a-new-application"></a>Registrera ett nytt program

1. Ge programmet ett visnings namn.

2. Ange en svars-URL. Den här informationen kan ändras senare, men om du känner till svars-URL: en för ditt program anger du det nu.

    ![Ny konfidentiell klient program registrering.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-behörigheter

Nästa Lägg till API-behörigheter:

1. Öppna **API-behörigheterna**:

    ![Konfidentiell klient. API-behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klicka på **Lägg till en behörighet**

3. Välj lämplig resurs-API:

    För Azure API för FHIR (hanterad tjänst) klickar du på **API: er som min organisation använder** och söker efter "Azure sjukvårds-API: er". För FHIR-servern med öppen källkod för Azure, välj din [FHIR API-resurs program registrering](register-resource-azure-ad-client-app.md):

    ![Konfidentiell klient. Mina API: er](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Välj omfång (behörigheter) som det konfidentiella programmet ska kunna begära för en användares räkning:

    ![Konfidentiell klient. Delegerade behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Apphemlighet

1. Skapa en program hemlighet (klient hemlighet):

    ![Konfidentiell klient. Program hemlighet](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Ange en beskrivning och varaktighet för hemligheten.

3. När den har skapats visas den bara en gång i portalen. Anteckna det och lagra det på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett konfidentiellt klient program i Azure Active Directory. Du är nu redo att distribuera [Azure API för FHIR](fhir-paas-powershell-quickstart.md).

När du har distribuerat Azure-API: et för FHIR kan du granska fler tillgängliga inställningar.
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-powershell-quickstart.md)