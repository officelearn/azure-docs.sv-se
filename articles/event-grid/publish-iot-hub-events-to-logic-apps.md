---
title: "Använd IoT-hubb händelser för att utlösa Azure Logic Apps | Microsoft Docs"
description: "Med routningstjänsten för händelsen i Azure händelse rutnät kan skapa automatiserade processer för att utföra åtgärder för Logikappar i Azure baserat på händelser för IoT-hubb."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: f54db95b0dfe5dc39c8e2a85375e56a93d1562ee
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps

Azure händelse rutnätet kan du ta hänsyn till händelser i IoT-hubb som utlöser åtgärder i underordnade business-program.

Den här artikeln beskriver hur en exempelkonfiguration som använder IoT-hubb och händelsen rutnätet. Du har en Azure logikapp ställts in för att skicka ett e-postmeddelande varje gång en enhet har lagts till i din IoT-hubb i slutet. 

## <a name="prerequisites"></a>Förutsättningar

* Ett e-postkonto från valfri provider som e-post som stöds av Azure Logikappar som Office 365 Outlook, Outlook.com eller Gmail. Kontot används för att skicka meddelanden om händelser. En fullständig lista över Logikapp kopplingar som stöds finns i [kopplingar: översikt](https://docs.microsoft.com/connectors/)
* Ett aktivt Azure-konto. Om du inte har någon, kan du [skapa ett kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/).
* En Iot-hubb i Azure. Om inte du ännu skapat något, se [Kom igång med IoT-hubb](../iot-hub/iot-hub-csharp-csharp-getstarted.md) för en genomgång. 

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa en logikapp och lägga till en händelseutlösare rutnät som övervakar resursgruppen för den virtuella datorn först. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för logik-app


1. I den [Azure-portalen](https://portal.azure.com)väljer **ny** > **Enterprise Integration** > **Logikapp**.

   ![Skapa en logikapp](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Namnge din logikapp som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 
3. När du är klar, Välj **fäst på instrumentpanelen**, och välj **skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **fäst på instrumentpanelen**, logikappen öppnas automatiskt i Logic Apps Designer. I annat fall kan du manuellt hitta och öppna logikappen.

4. I logik App Designer under **mallar**, Välj **tom Logikapp** så att du kan skapa din logikapp från början.

## <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en händelse som startar din logikapp. I den här självstudien utlösare som anger av arbetsflödet är en begäran tas emot via HTTP.  

1. Skriv i kopplingar och utlösare sökfältet **HTTP**.
2. Välj **begäran - när en HTTP-begäran tas emot** som utlösare. 

   ![Välj HTTP-begäran utlösare](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Välj **Använd exemplet nyttolast för att skapa schema**. 

   ![Välj HTTP-begäran utlösare](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Klistra in följande exempel JSON-kod i textrutan och välj sedan **klar**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
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
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. Du kan få ett popup-meddelande som står **Kom ihåg att inkludera en Content-Type-huvudet inställt till application/json i begäran.** På ett säkert sätt kan du ignorera den här och gå vidare till nästa avsnitt. 


### <a name="create-an-action"></a>Skapa en åtgärd

Åtgärder är eventuella åtgärder som sker efter utlösaren startar logik app arbetsflödet. För den här kursen är åtgärden att skicka ett e-postmeddelande från leverantören av e-post. 

1. Välj **nytt steg** sedan **lägga till en åtgärd**. 

   ![Nya kan du lägga till en åtgärd](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. Sök efter **e-post**. 
3. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Den här kursen använder **Office 365 Outlook**. Stegen för andra e-postleverantörer är liknande. 

   ![Välj e-provider-koppling](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Välj den **skickar ett e-** åtgärd. 
5. Om du uppmanas logga in på ditt e-postkonto. 
6. Skapa e-postmall. 
   * **Att**: Ange den e-postadressen för att ta emot e-postmeddelanden. Använd ett e-postkonto som du kan använda för att testa för den här självstudiekursen. 
   * **Ämne** och **brödtext**: skriva text för din e-post. Välj JSON egenskaper selector-verktyget för att inkludera dynamiskt innehåll baserat på informationen om händelsen.  

   E-postmall se ut som i det här exemplet:

   ![Fylla i e-information](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Spara din logikapp. 

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL

Innan du lämnar Logic Apps Designer, kopiera den URL som dina logic apps lyssnar på för en utlösare. Du kan använda den här URL: en för att konfigurera händelse rutnätet. 

1. Expandera den **när en HTTP-begäran tas emot** utlösaren configuration rutan genom att klicka på den. 
2. Kopiera värdet för **HTTP POST URL** genom att välja kopieringsknappen bredvid den. 

   ![Kopiera HTTP POST-URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Spara den här URL: en så att du kan referera till det i nästa avsnitt. 

## <a name="publish-an-event-from-iot-hub"></a>Publicera en händelse från IoT-hubb

I det här avsnittet kan du konfigurera din IoT-hubb för att publicera händelser när de inträffar. 

1. Navigera till din IoT-hubb i Azure-portalen. 
2. Välj **händelse rutnätet**.

   ![Öppna händelsen rutnätet-information](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Välj **händelseprenumerationen**. 

   ![Skapa ny händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Skapa händelseprenumerationen med följande värden: 
   * **Namnet**: Ange ett beskrivande namn.
   * **Prenumerera på alla händelsetyper**: avmarkerar du kryssrutan.
   * **Händelsetyper**: Välj **DeviceCreated**.
   * **Prenumeranten typen**: Välj **Web Hook**.
   * **Prenumeranten slutpunkt**: klistra in Webbadressen som du kopierade från din logikapp. 

   Du kan spara händelseprenumerationen här och ta emot meddelanden för varje enhet som har skapats i din IoT-hubb. För den här självstudiekursen, men vi använda för de valfria fälten för att filtrera för specifika enheter: 

   * **Prefixet filter**: Ange `devices/Building1_` att filtrera efter enhetshändelser i byggnad 1.
   * **Suffix filter**: Ange `_Temperature` att filtrera efter enhetshändelser relaterade till temperatur.

   När du är klar formuläret bör se ut som i följande exempel: 

   ![Exempelformulär händelse prenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Välj **skapa** spara händelseprenumerationen.

## <a name="create-a-new-device"></a>Skapa en ny enhet

Testa din logikapp genom att skapa en ny enhet för att utlösa en händelse e-postmeddelandet. 

1. Välj IoT-hubb **IoT-enheter**. 
2. Välj **Lägg till**.
3. För **enhets-ID**, ange `Building1_Floor1_Room1_Temperature`.
4. Välj **Spara**. 
5. Du kan lägga till flera enheter med olika enhets-ID för att testa händelsefilter för prenumerationen. Prova de här exemplen: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

När du har lagt till några enheter till din IoT-hubb, kontrollera din e-postadress om du vill se vilka som utlöste logikappen. 

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Du kan göra IoT-hubb-steg med hjälp av Azure CLI istället för att använda Azure-portalen. Mer information finns i Azure CLI-sidor för [skapar en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapar en IoT-enhet](https://docs.microsoft.com/cli/azure/iot/device)

## <a name="clean-up-resources"></a>Rensa resurser

Den här kursen används resurser som avgifter på din Azure-prenumeration. När du är klar provat självstudier och testar dina resultat, inaktivera eller ta bort resurser som du inte vill behålla. 

Om du inte vill förlora arbete på din logikapp kan du inaktivera det i stället för att ta bort den. 

1. Gå till din logikapp.
2. På den **översikt** bladet välj **ta bort** eller **inaktivera**. 

Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat ett ledigt NAV för den här kursen behöver du inte ta bort den för att förhindra avgifter.

1. Gå till din IoT-hubb. 
2. På den **översikt** bladet välj **ta bort**. 

Även om du behåller din IoT-hubb kan du vill ta bort händelseprenumerationen som du skapade. 

1. Välj i din IoT-hubb **händelse rutnätet**.
2. Välj den händelseprenumeration som du vill ta bort. 
3. Välj **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [reagera på IoT-hubb händelser med händelse rutnätet för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md).

Lär dig mer om vad du kan göra med [händelse rutnätet](overview.md).


