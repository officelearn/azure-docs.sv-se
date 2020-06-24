---
title: Självstudie om webbapp – klient programs konfiguration
description: Den här självstudien vägleder dig genom stegen för att registrera ett offentligt program för att förbereda för att distribuera ett webb program
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "84872643"
---
# <a name="client-application-registration"></a>Registrering av klient program
I den föregående själv studie kursen har du distribuerat och konfigurerat Azure API för FHIR. Nu när du har konfigurerat Azure API för FHIR-installationen kommer vi att registrera ett offentligt klient program. Du kan läsa igenom guiden fullständig [registrering av en offentlig klient](register-public-azure-ad-client-app.md) med instruktioner för mer information eller fel sökning, men vi har informat de viktigaste stegen för den här kursen nedan.

1. Navigera till Azure Active Directory
1. Välj **app Registration**  -->  **New Registration**
1. Namnge ditt program och konfigurera omdirigerings-URI: n tillhttps://www.getpostman.com/oauth2/callback


![Registrering av klient program](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Inställningar för klient program
När klient programmet har registrerats kopierar du program-ID: t (klient)-ID: t från översikts sidan. Du behöver det här värdet senare vid åtkomst till klienten.

![Kopiera app-ID](media/tutorial-web-app/app-id.png)

Ange sedan rätt autentiseringsalternativ. Välj **autentisering** från vänster sida. Markera rutorna **åtkomsttoken** och **ID-token** . Du kan också konfigurera omdirigerings-URI: n som förberedelse för när du skapar ditt webb program i den fjärde delen av den här självstudien. Det gör du genom att lägga till https:// \< Web-App-NAME>. azurewebsites.net i listan omdirigerings-URI. Om du väljer ett annat namn under steget där du [skriver din](tutorial-web-app-write-web-app.md)webbapp måste du komma tillbaka och uppdatera det.

![Inställningar för app-autentisering](media/tutorial-web-app/app-authentication.png)

Nu när du har konfigurerat rätt autentisering anger du API-behörigheterna. 
1. Välj **API-behörigheter** och klicka på **Lägg till en behörighet**
1. Under **API: er som min organisation använder**söker du efter Azure sjukvårds-API: er
1. Välj **user_impersonation** och klicka på **Lägg till behörigheter**

## <a name="next-steps"></a>Efterföljande moment
Nu har du ett offentligt klient program. I nästa självstudie kommer vi att gå igenom testningen och få åtkomst till det här programmet via Postman.

>[!div class="nextstepaction"]
>[Testa klient program i Postman](tutorial-web-app-test-postman.md)
