---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081538"
---
#### <a name="no-response-from-the-backend-service"></a>Inget svar från backend-tjänsten

När du testar lokalt kontrollerar du att backend-tjänsten körs och använder rätt port.

Om du testar mot **Azure API-appen**kontrollerar du att tjänsten körs och har distribuerats och har startats utan fel.

Se till att kontrol lera att du har angett bas adressen korrekt i **[Postman](https://www.postman.com/downloads)** eller i konfiguration av mobilapp när du testar via klienten. Bas adressen ska preliminärt vara `https://<api_name>.azurewebsites.net/` eller `https://localhost:5001/` när du testar lokalt.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Ta inte emot meddelanden på Android när du har startat eller stoppat en felsökningssession

Se till att du registrerar dig igen när du har startat eller stoppat en felsökningssession. Fel söknings programmet kommer att skapa en ny **Firebase** -token. Installationen av Notification Hub måste också uppdateras.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Tar emot status kod 401 från backend-tjänsten

Kontrol lera att du ställer in **apiKey** begär ande huvudet och att det här värdet matchar det som du har konfigurerat för backend-tjänsten.

Om du får det här felet när du testar lokalt kontrollerar du att det nyckel värde som du har definierat i klient konfigurationen matchar **autentiseringen: ApiKey** User-Setting som används av [API: et](#create-the-api-app).

Om du testar med en **API-app**kontrollerar du att nyckel värdet i klientens konfigurations fil matchar den **autentisering: ApiKey** program inställning som du använder i [API-appen](#create-the-api-app).

> [!NOTE]
> Om du har skapat eller ändrat den här inställningen efter att du har distribuerat backend-tjänsten måste du starta om tjänsten för att den ska börja gälla.

Om du väljer att inte slutföra kommandot [autentisera klienter med hjälp av en API-nyckel](#authenticate-clients-using-an-api-key-optional) , se till att du inte tillämpar attributet **auktorisera** på **NotificationsController** -klassen.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Tar emot status kod 404 från backend-tjänsten

Kontrol lera att metoden Endpoint och HTTP-begäran är korrekt. Till exempel bör slut punkterna preliminärt vara:

- **[Placering]**`https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[Ta bort]**`https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[Post]**`https://<api_name>.azurewebsites.net/api/notifications/requests`

Eller när du testar lokalt:

- **[Placering]**`https://localhost:5001/api/notifications/installations`
- **[Ta bort]**`https://localhost:5001/api/notifications/installations/<installation_id>`
- **[Post]**`https://localhost:5001/api/notifications/requests`

När du anger bas adressen i klient programmet, se till att den slutar med en `/` . Bas adressen ska preliminärt vara `https://<api_name>.azurewebsites.net/` eller `https://localhost:5001/` när du testar lokalt.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Det går inte att registrera och ett fel meddelande för Notification Hub visas

Kontrol lera att test enheten är ansluten till nätverket. Kontrol lera sedan http-svarets status kod genom att ange en Bryt punkt för att kontrol lera värdet för egenskapen **StatusCode** i **HttpResponse**.

Granska föregående fel söknings förslag där det är tillämpligt baserat på status koden.

Ange en Bryt punkt på de rader som returnerar dessa status koder för respektive API. Försök sedan att anropa backend-tjänsten när du felsöker lokalt.

Verifiera att backend-tjänsten fungerar som förväntat via **[Postman](https://www.postman.com/downloads)** med lämplig nytto Last. Använd den faktiska nytto lasten som skapats av klient koden för den aktuella plattformen.

Granska de plattformsspecifika konfigurations avsnitten för att se till att inga steg har missats. Kontrol lera att lämpliga värden matchas för `installation id` och `token` variabler för lämplig plattform.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Det går inte att lösa ett ID för enhets fel meddelandet visas

Granska de plattformsspecifika konfigurations avsnitten för att se till att inga steg har missats.
