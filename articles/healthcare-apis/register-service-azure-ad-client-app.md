---
title: Registrera en service app i Azure AD – Azure API för FHIR
description: Lär dig hur du registrerar ett tjänst klient program i Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 19d6b0ebfa2570b04c3a9dda3fe69428aa0eed75
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629331"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrera ett tjänst klient program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett tjänst klient program i Azure Active Directory. Klient program registreringar är Azure Active Directory representationer av program som kan användas för att autentisera och hämta token. En tjänst klient är avsedd att användas av ett program för att få en åtkomsttoken utan interaktiv autentisering av en användare. Den har vissa program behörigheter och använder en program hemlighet (lösen ord) för att hämta åtkomsttoken.

Följ dessa steg om du vill skapa en ny tjänst klient.

## <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. I [Azure Portal](https://portal.azure.com)navigerar du till **Azure Active Directory**.

2. Välj **Appregistreringar**.

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Välj **ny registrering**.

4. Ge tjänst klienten ett visnings namn. Tjänst klient program använder vanligt vis inte en svars-URL.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Ny registrering av tjänst klient program.":::

5. Välj **Register** (Registrera).

## <a name="api-permissions"></a>API-behörigheter

Nu när du har registrerat ditt program måste du välja vilka API-behörigheter som programmet ska kunna begära för användarens räkning:

1. Välj **API-behörigheter**.
1. Välj **Lägg till en behörighet**.

    Om du använder Azure-API: et för FHIR lägger du till en behörighet i Azure sjukvårds-API: erna genom att söka efter **Azure sjukvårds-API** : er under **API: er som används i organisationen**. 

    Om du refererar till ett annat resurs program väljer du den [FHIR-API resurs program registrering](register-resource-azure-ad-client-app.md) som du skapade tidigare under **Mina API: er**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Azure Portal. Ny registrering av tjänst klient program." lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Välj omfång (behörigheter) som det konfidentiella programmet ska kunna begära för en användares räkning:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Azure Portal. Ny registrering av tjänst klient program.":::

1. Bevilja medgivande till programmet. Om du inte har de behörigheter som krävs kan du kontakta Azure Active Directory-administratören:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Azure Portal. Ny registrering av tjänst klient program.":::

## <a name="application-secret"></a>Apphemlighet

Tjänst klienten måste ha en hemlighet (Password) för att få en token.

1. Välj **certifikat & hemligheter**.
2. Välj **Ny klienthemlighet**.

    ![Azure Portal. Tjänstens klient hemlighet](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Ange en beskrivning och varaktighet för hemligheten (antingen 1 år, 2 år eller aldrig).

4. När hemligheten har genererats visas den bara en gång i portalen. Anteckna och lagra på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett tjänst klient program i Azure Active Directory. Härnäst kan du lära dig mer om ytterligare inställningar för Azure API för FHIR.
 
>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)