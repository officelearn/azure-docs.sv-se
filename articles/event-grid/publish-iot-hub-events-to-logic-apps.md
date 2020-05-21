---
title: Självstudie – Använd IoT Hub händelser för att utlösa Azure Logic Apps
description: I den här självstudien visas hur du använder tjänsten för händelse dirigering i Azure Event Grid skapa automatiserade processer för att utföra Azure Logic Apps åtgärder baserat på IoT Hub händelser.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 0b1870af6316713590eec59aee2af94ce34b7e1a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722566"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Självstudie: skicka e-postaviseringar om Azure IoT Hub händelser med Event Grid och Logic Apps

Med Azure Event Grid kan du reagera på händelser i IoT Hub genom att utlösa åtgärder i underordnade företagsprogram.

Den här artikeln beskriver en exempel konfiguration som använder IoT Hub och Event Grid. I slutet har du konfigurerat en Azure Logic app som skickar ett e-postmeddelande varje gång en enhet läggs till i IoT Hub. 

## <a name="prerequisites"></a>Krav

* En aktiv Azure-prenumeration. Om du inte har någon prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

* Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps, till exempel Office 365 Outlook, Outlook.com eller Gmail. Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic app-kopplingar som stöds finns i [Översikt över anslutningar](https://docs.microsoft.com/connectors/).

  > [!IMPORTANT]
  > Innan du använder Gmail bör du kontrol lera om du har ett företags konto i G-serien (e-postadress med en anpassad domän) eller ett Gmail-användarkonto (e-postadress med @gmail.com eller @googlemail.com ). Endast företags konton i G-serien kan använda Gmail-anslutningen med andra anslutningar utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda Gmail-anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-app som ska användas för autentisering](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en Logic app och Lägg till en Event Grid-utlösare som övervakar resurs gruppen för den virtuella datorn. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**och skriver sedan "Logic app" i sökrutan och väljer RETUR. Välj **Logic app** från resultaten.

   ![Skapa en logikapp](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. På nästa skärm väljer du **skapa**. 

1. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 

   ![Fält för att skapa Logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Välj **Skapa**.

1. När resursen har skapats går du till din logikapp. Om du vill göra det väljer du **resurs grupper**och väljer sedan den resurs grupp som du skapade för den här självstudien. Leta sedan reda på Logic-appen i listan över resurser och markera den. 

1. I Logic Apps designer går du nedåt för att se **mallarna**. Välj **Tom Logic app** så att du kan bygga din Logic app från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.  

1. Skriv **HTTP** i sökfältet för anslutningsprogram och utlösare.

1. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Välj **Generera schemat genom att använda en exempelnyttolast**. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Klistra in följande JSON-exempelkod i textrutan och välj sedan **Klar**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt. 

### <a name="create-an-action"></a>Skapa en åtgärd

Åtgärder är alla steg som utförs när utlösaren har startat logikappens arbetsflöde. I den här självstudiekursen är åtgärden att skicka ett e-postmeddelande från din e-postleverantör. 

1. Välj **Nytt steg**. Då öppnas ett fönster där du kan **välja en åtgärd**.

1. Sök efter **E-post**.

1. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. I den här självstudien används **Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa. 

   ![Välj anslutningsprogram för e-postleverantör](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Välj åtgärden **Skicka ett e-postmeddelande**. 

1. Logga in på ditt e-postkonto om du uppmanas att göra det. 

1. Skapa e-postmallen. 

   * **Till**: Ange e-postadressen som meddelandena ska skickas till. I den här självstudiekursen använder du ett e-postkonto som du kan komma åt för testning. 

   * **Subject**: Fyll i texten för ämnet. När du klickar på text rutan ämne kan du välja dynamiskt innehåll som ska inkluderas. I den här självstudien används till exempel `IoT Hub alert: {event Type}` . Om du inte kan se dynamiskt innehåll, väljer du hyperlänken **Lägg till dynamiskt innehåll** – detta växlar till och från.

   * **Brödtext**: Skriv texten för e-postmeddelandet. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Om du inte kan se det dynamiska innehållet väljer du hyperlänken **Lägg till dynamiskt innehåll** under text rutan **brödtext** . Om du inte ser de fält som du vill ha klickar du på *mer* på skärmen med dynamiskt innehåll för att ta med fälten från föregående åtgärd.

   Din e-postmall kanske liknar den i det här exemplet:

   ![Fyll i e-postinformation](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Spara din logikapp. 

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps-designern kopierar du URL:en som används av dina logikappar för att lyssna efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid. 

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den. 

1. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det. 

   ![Kopiera HTTP POST-URL:en](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Spara den här URL:en så att du kan referera till den i nästa avsnitt. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar. 

1. Gå till din IoT-hubb på Azure Portal. Du kan göra detta genom att välja **resurs grupper**och sedan välja resurs gruppen för den här själv studie kursen och sedan välja din IoT Hub i listan över resurser.

2. Välj **händelser**.

   ![Öppna Event Grid-informationen](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Välj **Händelseprenumeration**. 

   ![Skapa ny händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Skapa händelseprenumerationen med följande värden: 

   * **Information om händelse prenumeration**: Ange ett beskrivande namn och välj **Event Grid schema**.

   * **Händelse typer**: Avmarkera alla val förutom **enheten som skapats**i **filtrera till händelse typer**.

       ![händelse typer för prenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Slut punkts information**: Välj slut punkts typ som **Web Hook** och välj *Välj en slut punkt* och klistra in den URL som du kopierade från din Logic app och bekräfta valet.

     ![webbadress till vald slutpunkt](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   När du är klar bör fönstret se ut som i följande exempel: 

    ![Exempelformulär för händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Du kan spara händelseprenumerationen här och få meddelanden för alla enheter som har skapats i IoT-hubben. I den här självstudien ska vi dock använda de valfria fälten för att filtrera efter specifika enheter. Välj **filter** överst i fönstret.

6. Välj **Lägg till nytt filter**. Fyll i fälten med följande värden:

   * **Nyckel**: Välj `Subject` .

   * **Operator**: Välj `String begins with` .

   * **Värde**: ange `devices/Building1_` om du vill filtrera enhets händelser i byggnad 1.
  
   Lägg till ett annat filter med följande värden:

   * **Nyckel**: Välj `Subject` .

   * **Operator**: Välj `String ends with` .

   * **Värde**: ange `_Temperature` om du vill filtrera enhets händelser relaterade till temperatur.

   Fliken **filter** i din händelse prenumeration bör nu se ut ungefär som den här bilden:

   ![Lägga till filter i händelse prenumerationen](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="create-a-new-device"></a>Skapa en ny enhet

Testa logikappen genom att skapa en ny enhet för att utlösa ett e-postmeddelande med en händelseavisering. 

1. Välj **IoT-enheter** från din IoT-hubb. 

2. Välj **Ny**.

3. Ange `Building1_Floor1_Room1_Light` för **Enhets-ID**.

4. Välj **Spara**. 

5. Du kan lägga till flera enheter med olika enhets-ID:n för att testa händelseprenumerationsfiltren. Prova de här exemplen: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Om du har lagt till de fyra exemplen bör listan över IoT-enheter se ut som på följande bild:

   ![IoT Hub enhets lista](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. När du har lagt till några enheter till IoT-hubben öppnar du din e-post för att se vilka som utlöste logikappen. 

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Om du vill kan du utföra IoT Hub-stegen med hjälp av Azure CLI i stället för att använda Azure Portal. Mer information finns i Azure CLI-sidorna för att [skapa en händelse prenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapa en IoT-enhet](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med att testa självstudien och testa dina resultat kan du inaktivera eller ta bort resurser som du inte vill behålla. 

Ta bort resurs gruppen om du vill ta bort alla resurser som skapats i den här självstudien. 

1. Välj **resurs grupper**och välj sedan den resurs grupp som du skapade för den här självstudien.

2. I fönstret resurs grupp väljer du **ta bort resurs grupp**. Du uppmanas att ange namnet på resurs gruppen och sedan kan du ta bort det. Alla resurser som ingår däri tas också bort.

Om du inte vill ta bort alla resurser kan du hantera dem en i taget. 

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den. 

1. Gå till logikappen.

2. Välj **Ta bort** eller **Inaktivera** på bladet **Översikt**. 

Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

1. Gå till IoT-hubben. 

2. Välj **Ta bort** på bladet **Översikt**. 

Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade. 

1. Välj **Event Grid** i IoT-hubben.

2. Välj den händelseprenumeration som du vill ta bort. 

3. Välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du kan svara på IoT Hub-händelser med hjälp av Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).
* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lär dig mer om vad du kan göra med [Event Grid](overview.md).