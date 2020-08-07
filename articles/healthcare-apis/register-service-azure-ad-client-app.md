---
title: Registrera en service app i Azure AD – Azure API för FHIR
description: Lär dig hur du registrerar ett tjänst klient program i Azure Active Directory som kan användas för att autentisera och hämta token.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 34eec3ad0d2fc193744898b6f08cbe50c261c945
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853032"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrera ett tjänst klient program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett tjänst klient program i Azure Active Directory. Klient program registreringar är Azure Active Directory representationer av program som kan användas för att autentisera och hämta token. En tjänst klient är avsedd att användas av ett program för att få en åtkomsttoken utan interaktiv autentisering av en användare. Den har vissa program behörigheter och använder en program hemlighet (lösen ord) för att hämta åtkomsttoken.

Följ stegen nedan för att skapa en ny tjänst klient.

## <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på [Azure Portal](https://portal.azure.com).

2. I bladet **Azure Active Directory** klickar du på **Appregistreringar**:

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka **Ny registrering**.

## <a name="service-client-application-details"></a>Information om tjänstens klient program

* Tjänst klienten behöver ett visnings namn och du kan också ange en svars-URL, men den används vanligt vis inte.

    ![Azure Portal. Ny registrering av tjänst klient program.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-behörigheter

Du måste tilldela tjänstens klient program roller. 

1. Öppna **API-behörigheterna** och välj din [FHIR-API-resurs program registrering](register-resource-azure-ad-client-app.md). Om du använder Azure-API: et för FHIR lägger du till en behörighet i Azure sjukvårds-API: erna genom att söka efter Azure sjukvårds-API: er under **API: er som används i organisationen**.

    ![Azure Portal. API-behörigheter för tjänst klient](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Välj de program roller som du har definierat i resurs programmet:

    ![Azure Portal. Tjänst klient program behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Bevilja medgivande till programmet. Om du inte har de behörigheter som krävs kan du kontakta Azure Active Directory-administratören:

    ![Azure Portal. Godkännande av tjänst klient administratör](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Apphemlighet

Tjänst klienten behöver en hemlighet (lösen ord) som du kommer att använda när du hämtar tokens.

1. Klicka på **certifikat &amp; hemligheter**

2. Klicka på **Ny klienthemlighet**

    ![Azure Portal. Tjänstens klient hemlighet](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Ange varaktigheten för hemligheten.

4. När den har skapats visas den bara en gång i portalen. Anteckna och lagra på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett tjänst klient program i Azure Active Directory. Sedan distribuerar du ett FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera FHIR-server med öppen källkod](fhir-oss-powershell-quickstart.md)