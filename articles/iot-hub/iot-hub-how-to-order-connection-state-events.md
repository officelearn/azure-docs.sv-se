---
title: Ordna enhetshändelser anslutning från Azure IoT Hub med Azure Cosmos DB | Microsoft Docs
description: Den här artikeln beskriver hur du ordna och registrera enheten anslutningshändelser från Azure IoT Hub med Azure Cosmos DB för att upprätthålla senaste anslutningsstatus
services: iot-hub
documentationcenter: ''
author: ash2017
manager: briz
editor: ''
ms.service: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: dd3c750e93646624e46c46afd871ef75af905bf0
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145874"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordna enheten anslutningshändelser från Azure IoT Hub med Azure Cosmos DB

Azure Event Grid kan du skapa händelsebaserade program och enkelt integrera IoT-händelser i dina affärslösningar. Den här artikeln beskriver hur du en uppsättning som kan användas för att spåra och lagra det senaste anslutningen enhetstillståndet i Cosmos DB. Vi använder sekvensnummer som är tillgängliga i händelserna enheten ansluten och enheten frånkopplad och lagra det senaste tillståndet i Cosmos DB. Vi ska använda en lagrad procedur är ett programlogik som körs mot en samling i Cosmos DB.

Sekvensnumret är en strängrepresentation av ett hexadecimalt tal. Du kan använda jämför sträng för att identifiera det större antalet. Om du konverterar strängen som ska hexadecimal ska talet vara ett 256-bitars tal. Sekvensnumret strikt ökar och den senaste händelsen kommer att ha högre nummer än andra händelser. Detta är användbart om du har ofta enheten ansluter och kopplar från och se till att endast den senaste händelsen används för att utlösa en underordnad åtgärd som Azure Event Grid har inte stöd för sorteringen av händelser.

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt Azure-konto. Om du inte redan har ett konto kan du [skapa ett kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/).
* Ett aktivt Azure Cosmos DB SQL API-konto. Om du inte har skapat en ännu, se [skapa ett databaskonto](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account) en genomgång.
* En samling i din databas. Se [Lägg till en samling](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection) en genomgång.
* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md). 

## <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

Först skapar du en lagrad procedur och ställas in att köra en logik som jämför sekvensnummer inkommande och registrerar den senaste händelsen per enhet i databasen.

1. Välj i din Cosmos DB SQL API **Datautforskaren** > **objekt** > **ny lagringsprocedur**.

   ![Skapa lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Ange en lagrad procedur-id och klistra in följande i själva ”lagrade proceduren”. Observera att den här koden bör ersätter eventuella befintliga koden i själva lagrad procedur. Den här koden har en rad per enhets-ID och registrerar senaste anslutningsstatus för enhets-id genom att identifiera det högsta numret. 

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};
      
      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Spara den lagrade proceduren: 

    ![Spara lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn. 

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I den [Azure-portalen](https://portal.azure.com)väljer **New** > **integrering** > **Logikapp**.

   ![Skapa en logikapp](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb. 
3. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **Fäst på instrumentpanelen** öppnas logikappen automatiskt i Logic Apps-designern. Om inte kan du leta upp och öppna logikappen manuellt.

4. Välj **Tom logikapp** under **Mallar** i Logic Apps-designern, så att du kan skapa din logikapp från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.  

1. Skriv **HTTP** i sökfältet för anslutningsprogram och utlösare.
2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare. 

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**. 

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Klistra in följande JSON-exempelkod i textrutan och välj sedan **Klar**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt. 

### <a name="create-a-condition"></a>Skapa ett villkor

Villkor hjälp med att köra specifika åtgärder efter att skicka ett specifikt villkor du är logikappens arbetsflöde. När villkoret är uppfyllt, kan du definiera en önskad åtgärd. Villkoret är att kontrollera om händelsetyp är enhet ansluten eller frånkopplad-enhet i den här självstudien. Åtgärden är att köra den lagrade proceduren i databasen. 

1. Välj **nytt steg** sedan **Built-ins** och **villkor**. 

2. Fyll villkoret som visas nedan för att utföra detta endast för enheten ansluten och enheten frånkopplad händelser:
  * Välj ett värde: **händelsetyp**
  * Ändra är lika med ”att **slutar med**
  * Välj ett värde: **nected**

   ![Fyll villkor](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. Om villkoret är sant, klickar du på **Lägg till en åtgärd**.
  
   ![Lägg till åtgärd om värdet är true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. Sök efter Cosmos DB och klicka på **Azure Cosmos DB – Kör lagrad procedur**

   ![Sök efter CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. Fyll i formuläret för att utföra åtgärden lagras skaffa genom att välja värden från databasen. Ange partitionsnyckelvärde och parametrar som visas nedan. 

   ![Fyll i logikappens](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Spara din logikapp. 

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps-designern kopierar du URL:en som används av dina logikappar för att lyssna efter en utlösare. Du använder den här URL:en för att konfigurera Event Grid. 

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den. 
2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det. 

   ![Kopiera HTTP POST-URL:en](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar. 

1. Gå till din IoT-hubb på Azure Portal. 
2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Välj **Händelseprenumeration**. 

   ![Skapa ny händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Skapa händelseprenumerationen med följande värden: 
   * **Händelsetyp**: avmarkera prenumerera på alla händelsetyper och välj **enheten ansluten** och **enheten frånkopplad** på menyn.
   * **Information om slutpunkten**: Välj typ av slutpunkt som **Webhook** och klicka på Välj slutpunkt och klistra in den URL som du kopierade från din logikapp och bekräfta valet.

   ![Välj slutpunkts-url](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **Information om händelse-prenumeration**: Ange ett beskrivande namn och välj **Grid Händelseschema** när du är klar formuläret bör se ut som i följande exempel: 

   ![Exempelformulär för händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="observe-events"></a>Se händelser
Nu när din händelseprenumeration är konfigurerat kan vi testa genom att ansluta en enhet.

### <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

1. Välj **IoT-enheter** från din IoT-hubb. 
2. Välj **Lägg till**.
3. Ange `Demo-Device-1` för **Enhets-ID**.
4. Välj **Spara**. 
5. Du kan lägga till flera enheter med olika enhets-ID.

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kopiera den **anslutningssträng – primärnyckel** för senare användning.

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Starta simulatorn Raspberry Pi

1. Nu ska vi använda Raspberry Pi web simulatorn för att simulera enhetsanslutning.

[Starta simulatorn Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-applciation-on-the-raspberry-pi-web-simulator"></a>Kör en exempel-hemsidan på web-simulator Raspberry Pi
Detta utlöser en ansluten enhet-händelse.

1. I området för kodning, ersätter du platshållaren i rad 15 med Azure IoT Hub enhetens anslutningssträng.

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Kör programmet genom att klicka på **kör**.

Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicka på **stoppa** att stoppa simulator och utlöser en **enheten frånkopplad** händelse.

Du har nu kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb. 

### <a name="observe-events-in-cosmos-db"></a>Se händelser i Cosmos DB

Du kan se resultatet av den lagrade proceduren som körs i ditt Cosmos DB-dokument. Här är hur den ser ut. Observera att varje rad innehåller senaste anslutning enhetstillstånd per enhet

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Om du vill kan du utföra IoT Hub-stegen med hjälp av Azure CLI i stället för att använda Azure Portal. Mer information finns i Azure CLI-sidor för [skapa en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapar en IoT-enhet](https://docs.microsoft.com/cli/azure/iot/device).

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

Att ta bort ett Azure Cosmos DB-konto från Azure-portalen höge-Klicka på namnet på kontot och klicka på **ta bort konto**. Se detaljerade anvisningar för hur [tar bort ett Azure Cosmos DB-konto](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md)
* [Prova guiden för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* Läs om vad du kan göra med [Event Grid](../event-grid/overview.md)


