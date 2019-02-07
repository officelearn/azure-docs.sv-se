---
title: Registrera ett tjänstprogram i Azure Active Directory - Azure-API för FHIR
description: Den här artikeln beskrivs hur du registrerar ett tjänstprogram i Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824293"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrera ett klientprogram för tjänsten i Azure Active Directory

I den här artikeln lär du dig att registrera ett klientprogram för tjänsten i Azure Active Directory. Klienten programregistreringar är Azure Active Directory representationer av program som kan användas för att autentisera och hämta token. Klienten för tjänsten är avsedd att användas av ett program för att hämta en åtkomsttoken utan interaktiv autentisering för en användare. Installationsprogrammet har vissa behörigheter för programmet och använder en programhemlighet (lösenord) när hämta åtkomsttoken.

Följ stegen nedan för att skapa en ny service-klient.

## <a name="app-registrations-in-azure-portal"></a>App-registreringar i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**.

2. I den **Azure Active Directory** bladet klickar du på **appregistreringar (förhandsversion)**:

    ![Azure-portalen. Ny Appregistrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på **ny registrering**.

## <a name="service-client-application-details"></a>Tjänstinformation klienten program

* Tjänstklienten behöver ett visningsnamn och du kan också tillhandahålla en svars-URL, men det vanligtvis inte ska användas.

    ![Azure-portalen. Ny Appregistrering för tjänsten i klienten.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-behörigheter

Du måste bevilja tjänstklienten programroller. 

1. Öppna den **API-behörigheter** och välj din [FHIR API-resursen programregistrering](register-resource-azure-ad-client-app.md):

    ![Azure-portalen. Klient-API för Tjänstbehörigheter](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Välj programroller dig från de som är definierade på resursprogrammet:

    ![Azure-portalen. Behörigheter för klienten tjänstprogram](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Ge ditt medgivande till programmet. Om du inte har de behörigheter som krävs, kontrollera med din Azure Active Directory-administratör:

    ![Azure-portalen. Tjänsten klienten administratörens godkännande](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Programhemlighet

Tjänstklienten måste en hemlighet (lösenord) som du använde när du hämta token.

1. Klicka på **certifikat &amp; hemligheter**

2. Klicka på **nya klienthemlighet**

    ![Azure-portalen. Klienthemlighet för tjänstens](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Ange en varaktighet på hemligheten.

4. När den har skapats kan visas det bara en gång i portalen. Anteckna den och lagra på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett klientprogram för tjänsten i Azure Active Directory. Distribuera ett FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-oss-powershell-quickstart.md)