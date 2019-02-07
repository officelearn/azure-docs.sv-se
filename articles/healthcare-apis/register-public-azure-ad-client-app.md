---
title: Registrera ett program för offentlig klient i Azure Active Directory - Azure-API för FHIR
description: Den här artikeln beskrivs hur du registrerar ett offentliga klientprogram i Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824281"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrera ett program för offentlig klient i Azure Active Directory

I den här artikeln lär du dig att registrera ett offentligt program i Azure Active Directory. Klienten programregistreringar är Azure Active Directory representationer av program som kan autentisera och be om API-behörigheter för en användares räkning. Offentliga klienter är program, till exempel mobila program och en sida javascript-program som inte kan hålla hemligheter konfidentiell. Metoden liknar [registrerar en konfidentiell klient](register-confidential-azure-ad-client-app.md), men eftersom offentliga klienter inte får vara betrodd att lagra en programhemlighet, det finns inget behov att lägga till ett.

## <a name="app-registrations-in-azure-portal"></a>App-registreringar i Azure-portalen

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**.

2. I den **Azure Active Directory** bladet klickar du på **appregistreringar (förhandsversion)**:

    ![Azure-portalen. Ny Appregistrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **ny registrering**.

## <a name="application-registration-overview"></a>Översikt över registrering

1. Ge programmet med ett visningsnamn.

2. Ange en svars-URL. Svars-URL är där koder för autentisering skickas tillbaka till klientprogrammet. Du kan lägga till fler-URL: Svarswebbadresser och redigera befintliga senare.

    ![Azure-portalen. Ny offentlig Appregistrering.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-behörigheter

På samma sätt till den [konfidentiell klientprogram](register-confidential-azure-ad-client-app.md), måste du välja vilka API-behörigheter det här programmet ska kunna begära åt användare:

1. Öppna den **API-behörigheter** och välj din [FHIR API-resursen programregistrering](register-resource-azure-ad-client-app.md):

    ![Azure-portalen. Ny offentlig API-behörigheter.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. Välj scope som du vill att programmet ska kunna begära.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar en offentlig klient-program i Azure Active Directory. Distribuera ett FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-oss-powershell-quickstart.md)
