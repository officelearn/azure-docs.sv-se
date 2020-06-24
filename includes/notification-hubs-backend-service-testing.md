---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72e4318bea7245e440db4c7d95bf7f1f38bbe268
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081535"
---
### <a name="send-a-test-notification"></a>Skicka ett testmeddelande

1. Öppna en ny flik i [Postman](https://www.postman.com/downloads/).

1. Ange begäran att **publicera**och ange följande adress:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Om du väljer att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) måste du konfigurera begärandehuvuden så att de inkluderar ditt **apiKey** -värde.

   | Tangent                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

   > [!NOTE]
   > Detta finns i **config. local_secrets. cs** inifrån **PushDemo** -projektet.

1. Välj alternativet **RAW** för **bröd texten**, välj sedan **JSON** i listan med format alternativ och ta med en plats hållare **JSON** -innehåll:

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Välj knappen **kod** , som finns under knappen **Spara** längst upp till höger i fönstret. Begäran bör se ut ungefär som i följande exempel när den visas för **HTML** (beroende på om du har inkluderat ett **apiKey** -huvud):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Kör **PushDemo** -programmet på en eller båda av mål plattformarna (**Android** och **iOS**).

    > [!NOTE]
    > Om du testar på **Android** ser du till att du inte kör i **fel sökning**, eller om appen har distribuerats genom att köra programmet, tvinga sedan stänga appen och starta den igen från Start programmet.

1. Tryck på knappen **Registrera** i **PushDemo** -appen.

1. Gå tillbaka till **[Postman](https://www.postman.com/downloads)** och Stäng fönstret **generera kodfragment** (om du inte redan har gjort det) klickar du på knappen **Skicka** .

1. Verifiera att du får ett **200 OK** -svar i **[Postman](https://www.postman.com/downloads)** och att aviseringen visas i appen som visar **Åtgärds åtgärden mottagen**.  

1. Stäng **PushDemo** -appen och klicka sedan på knappen **Skicka** igen i **[Postman](https://www.postman.com/downloads)**.

1. Verifiera att du får ett **200 OK** -svar i **[Postman](https://www.postman.com/downloads)** igen. Verifiera att ett meddelande visas i meddelande fältet för **PushDemo** -appen med rätt meddelande.

1. Tryck på meddelandet för att bekräfta att det öppnar appen och visar aviseringen **Åtgärds mottagen åtgärd** .

1. Tillbaka i **[Postman](https://www.postman.com/downloads)**, ändra föregående begär ande text för att skicka ett tyst meddelande som anger *action_b* i stället för *action_a* för **åtgärd** svärdet.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. När appen fortfarande är öppen klickar du på knappen **Skicka** i **[Postman](https://www.postman.com/downloads)**.

1. Kontrol lera att du får ett **200 OK** -svar i **[Postman](https://www.postman.com/downloads)** och att aviseringen visas i appen som visar **ActionB-åtgärd som mottagits** i stället för **Åtgärds åtgärd mottagen**.

1. Stäng **PushDemo** -appen och klicka sedan på knappen **Skicka** igen i **[Postman](https://www.postman.com/downloads)**.

1. Verifiera att du får ett **200 OK** -svar i **[Postman](https://www.postman.com/downloads)** och att det tysta meddelandet inte visas i meddelande fältet.
