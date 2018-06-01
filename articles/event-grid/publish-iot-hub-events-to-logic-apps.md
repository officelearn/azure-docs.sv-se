---
title: Använda IoT Hub-händelser för att utlösa Azure Logic Apps | Microsoft Docs
description: Använd händelseroutningstjänsten i Azure Event Grid och skapa automatiserade processer för att utföra Azure Logic Apps-åtgärder baserade på IoT Hub-händelser.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: aab674f16fcc3fd4869f24f72f66878a8751d892
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301492"
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps

Med Azure Event Grid kan du reagera på händelser i IoT Hub genom att utlösa åtgärder i underordnade företagsprogram.

Den här artikeln går igenom en exempelkonfiguration som använder IoT Hub och Event Grid. Du lär dig hur du konfigurerar en Azure-logikapp att skicka ett e-postmeddelande varje gång en enhet läggs till i din IoT-hubb. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps, t.ex. Office 365 Outlook, Outlook.com eller Gmail. Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic App-anslutningsprogram som stöds finns i [Översikt över anslutningsappar](https://docs.microsoft.com/connectors/)
* Ett aktivt Azure-konto. Om du inte redan har ett konto kan du [skapa ett kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/).
* En Iot-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp


1. På [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Enterprise-integration** > **Logikapp**.

   ![Skapa en logikapp](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 
3. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt i Logic Apps-designern. Om inte kan du leta upp och öppna logikappen manuellt.

4. Välj **Tom logikapp** under **Mallar** i Logic Apps-designern, så att du kan skapa din logikapp från grunden.

## <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.  

1. Skriv **HTTP** i sökfältet för anslutningsprogram och utlösare.
2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**. 

   ![Välj utlösare för HTTP-begäranden](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Klistra in följande JSON-exempelkod i textrutan och välj sedan **Klar**:

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
5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt. 


### <a name="create-an-action"></a>Skapa en åtgärd

Åtgärder är alla steg som utförs när utlösaren har startat logikappens arbetsflöde. I den här självstudiekursen är åtgärden att skicka ett e-postmeddelande från din e-postleverantör. 

1. Välj **Nytt steg** och sedan **Lägg till en åtgärd**. 

   ![Nytt steg, Lägg till en åtgärd](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. Sök efter **E-post**. 
3. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. I den här självstudiekursen används **Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa. 

   ![Välj anslutningsprogram för e-postleverantör](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Välj åtgärden **Skicka ett e-postmeddelande**. 
5. Logga in på ditt e-postkonto om du uppmanas att göra det. 
6. Skapa e-postmallen. 
   * **Till**: Ange e-postadressen som meddelandena ska skickas till. I den här självstudiekursen använder du ett e-postkonto som du kan komma åt för testning. 
   * **Ämne** och **Brödtext**: Skriv e-postmeddelandets text. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata.  

   Din e-postmall kanske liknar den i det här exemplet:

   ![Fyll i e-postinformation](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Spara din logikapp. 

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps-designern kopierar du URL:en som används av dina logikappar för att lyssna efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid. 

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den. 
2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det. 

   ![Kopiera HTTP POST-URL:en](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt. 

## <a name="publish-an-event-from-iot-hub"></a>Publicera en händelse från IoT Hub

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar. 

1. Gå till din IoT-hubb på Azure Portal. 
2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Välj **Händelseprenumeration**. 

   ![Skapa ny händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Skapa händelseprenumerationen med följande värden: 
   * **Namn**: Ange ett beskrivande namn.
   * **Prenumerera på alla händelsetyper**: Avmarkera kryssrutan.
   * **Händelsetyper**: Välj **DeviceCreated**.
   * **Typ av prenumerant**: Välj **Webhook**.
   * **Slutpunkt för prenumerant**: Klistra in URL:en som du kopierade från logikappen. 

   Du kan spara händelseprenumerationen här och få meddelanden för alla enheter som har skapats i IoT-hubben. I den här självstudiekursen ska vi dock använda de valfria fälten för att filtrera fram specifika enheter: 

   * **Prefixfilter**: Ange `devices/Building1_` för att filtrera fram enhetshändelser i byggnad 1.
   * **Suffixfilter**: Ange `_Temperature` för att filtrera fram enhetshändelser relaterade till temperatur.

   När du är klar bör formuläret likna det i följande exempel: 

   ![Exempelformulär för händelseprenumeration](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="create-a-new-device"></a>Skapa en ny enhet

Testa logikappen genom att skapa en ny enhet för att utlösa ett e-postmeddelande med en händelseavisering. 

1. Välj **IoT-enheter** från din IoT-hubb. 
2. Välj **Lägg till**.
3. Ange `Building1_Floor1_Room1_Temperature` för **Enhets-ID**.
4. Välj **Spara**. 
5. Du kan lägga till flera enheter med olika enhets-ID:n för att testa händelseprenumerationsfiltren. Prova de här exemplen: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

När du har lagt till några enheter till IoT-hubben öppnar du din e-post för att se vilka som utlöste logikappen. 

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Om du vill kan du utföra IoT Hub-stegen med hjälp av Azure CLI i stället för att använda Azure Portal. Mer information finns på Azure CLI-sidorna om hur du [skapar en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och hur du [skapar en IoT-enhet](https://docs.microsoft.com/cli/azure/iot/device)

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med kursen och har testat resultaten bör du därför inaktivera eller ta bort resurser som du inte vill behålla. 

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

Lär dig mer om [hur du kan svara på IoT Hub-händelser med hjälp av Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).

Lär dig mer om vad du kan göra med [Event Grid](overview.md).


