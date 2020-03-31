---
title: Beställ enhetsanslutningshändelser fr Azure IoT Hub med Azure Cosmos DB
description: I den hÃ¤r artikeln beskrivs hur du beställer och registrerar enhetsanslutningshändelser frÃ¥n Azure IoT Hub med Azure Cosmos DB fÃ¶r att upprätthålla det senaste anslutningstillståndet
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954258"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Begär enhetsanslutningshändelser från Azure IoT Hub med hjälp av Azure Cosmos DB

Azure Event Grid hjälper dig att skapa händelsebaserade program och enkelt integrera IoT-händelser i dina affärslösningar. Den här artikeln går igenom en installation som kan användas för att spåra och lagra det senaste enhetsanslutningstillståndet i Cosmos DB. Vi kommer att använda sekvensnumret som är tillgängligt i händelserna för enhetsansluten och enhet frånkopplad och lagra det senaste tillståndet i Cosmos DB. Vi kommer att använda en lagrad procedur, vilket är en programlogik som körs mot en samling i Cosmos DB.

Sekvensnumret är en strängrepresentation av ett hexadecimalt tal. Du kan använda strängjämförelse för att identifiera det större talet. Om du konverterar strängen till hex blir numret ett 256-bitarsnummer. Sekvensnumret ökar strikt och den senaste händelsen kommer att ha ett högre antal än andra händelser. Detta är användbart om du har frekventa enhetsanslutningar och kopplar från och vill se till att endast den senaste händelsen används för att utlösa en nedströmsåtgärd, eftersom Azure Event Grid inte stöder beställning av händelser.

## <a name="prerequisites"></a>Krav

* Ett aktivt Azure-konto. Om du inte har ett kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Ett aktivt Azure Cosmos DB SQL API-konto. Om du inte har skapat något ännu läser du [Skapa ett databaskonto](../cosmos-db/create-sql-api-java.md#create-a-database-account) för en genomgång.

* En samling i databasen. Se [Lägga till en samling](../cosmos-db/create-sql-api-java.md#add-a-container) för en genomgång. När du skapar samlingen `/id` använder du för partitionsnyckeln.

* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

Skapa först en lagrad procedur och konfigurera den för att köra en logik som jämför sekvensnummer för inkommande händelser och registrerar den senaste händelsen per enhet i databasen.

1. I cosmos DB SQL API väljer du Nya lagrade **datautforskarenobjekt** > **Items** > **New Stored Procedure**.

   ![Skapa lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Ange **LatestDeviceConnectionState** för det lagrade procedur-ID:t och klistra in följande i **brödtexten Lagrad procedur**. Observera att den här koden bör ersätta all befintlig kod i den lagrade procedurkroppen. Den här koden upprätthåller en rad per enhets-ID och registrerar det senaste anslutningstillståndet för det enhets-ID:et genom att identifiera det högsta sekvensnumret.

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

    ![spara lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Skapa en logikapp

Börja med att skapa en logikapp och lägg till en utlösare för händelserutnät som övervakar resursgruppen för den virtuella datorn.

### <a name="create-a-logic-app-resource"></a>Skapa en resurs för en logikapp

1. I [Azure-portalen](https://portal.azure.com)väljer du **+Skapa en resurs**, väljer **Integration** och sedan **Logic App**.

   ![Skapa en logikapp](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb.

   ![Ny logikapp](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Välj **Skapa** om du vill skapa logikappen.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE]
   > Om du vill söka efter och öppna logikappen igen väljer du **Resursgrupper** och väljer den resursgrupp som du använder för den här in-till-mig.Find and open your logic app again, select Resource groups and select the resource group you are using for this how-to. Välj sedan din nya logikapp. Då öppnas Logic App Designer.

4. Bläddra åt höger i Logic App Designer tills du ser vanliga utlösare. Under **Mallar**väljer du **Tom Logikapp** så att du kan skapa logikappen från grunden.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.

1. Skriv **HTTP** i sökfältet för kopplingar och utlösare och tryck på Retur.

2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare.

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**.

   ![Använd exempelnyttolasten för att generera ett schema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Klistra in exempel på JSON-nyttolast](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt.

### <a name="create-a-condition"></a>Skapa ett villkor

I logikappens arbetsflöde hjälper villkor till att köra specifika åtgärder efter att du har passerat det specifika villkoret. När villkoret är uppfyllt kan en önskad åtgärd definieras. För den här självstudien är villkoret att kontrollera om eventType är enhetsansluten eller enheten frånkopplad. Åtgärden kommer att vara att köra den lagrade proceduren i databasen.

1. Välj **+ Nytt steg** sedan **Inbyggt**och leta sedan reda på och välj **Villkor**. Klicka i **Välj ett värde** så visas en ruta med dynamiskt innehåll – de fält som kan väljas. Fyll i fälten som visas nedan för att bara köra detta för enhetsanslutna och enhet frånkopplad händelser:

   * Välj ett värde: **eventType** – välj det här i fälten i det dynamiska innehållet som visas när du klickar på det här fältet.
   * Ändra "är lika med" till **slutar med**.
   * Välj ett värde: **nected**.

     ![Fyll villkor](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Klicka på Lägg till **en åtgärd**i dialogrutan **Om sant** .
  
   ![Lägg till åtgärd om det är sant](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Sök efter Cosmos DB och välj **Azure Cosmos DB - Kör lagrad procedur**

   ![Sök efter CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Fyll i **cosmosdb-anslutning** för **anslutningsnamnet** och välj posten i tabellen och välj sedan **Skapa**. Du ser panelen **Kör lagrad procedur.** Ange värdena för fälten:

   **Databas-ID:** ToDoList

   **Insamlings-ID:** Artiklar

   **Sproc ID:** LatestDeviceConnectionState

5. Välj **Lägg till ny parameter**. I listrutan som visas markerar du rutorna **bredvid Partitionsnyckel** **och Parametrar för den lagrade proceduren**och klickar sedan någon annanstans på skärmen. Det lägger till ett fält för partitionsnyckelvärde och ett fält för parametrar för den lagrade proceduren.

   ![fylla logikappåtgärd](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Ange nu partitionsnyckelvärdet och parametrarna som visas nedan. Var noga med att sätta i parentes och dubbel-citat som visas. Du kanske måste klicka på **Lägg till dynamiskt innehåll** för att få de giltiga värden som du kan använda här.

   ![fylla logikappåtgärd](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Kontrollera att **brödtexten** är markerad under **Välj en utdata från föregående steg**högst upp i fönstret där det står För **varje**.

   ![fylla logikapp för varje](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Spara din logikapp.

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Innan du lämnar Logic Apps Designer kopierar du webbadressen som logikappen lyssnar på för en utlösare. Du använder den här URL:en för att konfigurera Event Grid.

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den.

2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det.

   ![Kopiera HTTP POST-URL:en](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar.

1. Gå till din IoT-hubb på Azure Portal.

2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Välj **+ Händelseprenumeration**.

   ![Skapa ny händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Fyll i **information om händelseprenumeration:** Ange ett beskrivande namn och välj **Schema för händelserutnät**.

5. Fyll i fälten **Händelsetyper.** I listrutan väljer du bara **Enhetsansluten** och **Enhet frånkopplad** från menyn. Klicka någon annanstans på skärmen för att stänga listan och spara dina val.

   ![Ange att händelsetyper ska söka efter](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. För **Slutpunktsinformation**väljer du Slutpunktstyp som **webbkrok** och klickar på välj slutpunkt och klistra in url:en som du kopierade från logikappen och bekräfta valet.

   ![Välj url till slutpunkt](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Formuläret ska nu se ut ungefär som i följande exempel:

   ![Exempelformulär för händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="observe-events"></a>Observera händelser

Nu när din händelseprenumeration har konfigurerats ska vi testa genom att ansluta en enhet.

### <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

1. Välj **IoT-enheter** från din IoT-hubb.

2. Välj **+Lägg till** högst upp i fönstret.

3. Ange `Demo-Device-1` för **Enhets-ID**.

4. Välj **Spara**.

5. Du kan lägga till flera enheter med olika enhets-ID: er.

   ![Enheter som läggs till i navet](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klicka på enheten igen; nu kommer anslutningssträngarna och tangenterna att fyllas i. Kopiera **anslutningssträngen – primärnyckeln** för senare användning.

   ![ConnectionString för enhet](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Starta Raspberry Pi simulator

Låt oss använda Hallberry Pi webbsimulatorn för att simulera enhetsanslutning.

[Starta Raspberry Pi simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Kör ett exempelprogram på Raspberry Pi-webbsimulatorn

Detta utlöser en enhetsansluten händelse.

1. I kodningsområdet ersätter du platshållaren i linje 15 med din Azure IoT Hub-enhetsanslutningssträng som du sparade i slutet av föregående avsnitt.

   ![Klistra in i enhetsanslutningssträng](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Kör programmet genom att välja **Kör**.

Du ser något som liknar följande utdata som visar sensordata och meddelanden som skickas till din IoT-hubb.

   ![Köra programmet](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicka på **Stoppa** om du vill stoppa simulatorn och utlösa en enhet **frånkopplad** händelse.

Du har nu kört ett exempelprogram för att samla in sensordata och skicka det till din IoT-hubb.

### <a name="observe-events-in-cosmos-db"></a>Observera händelser i Cosmos DB

Du kan se resultatet av den utförda lagrade proceduren i ditt Cosmos DB-dokument. Så här ser det ut. Varje rad innehåller det senaste enhetsanslutningstillståndet per enhet.

   ![Hur resultatet](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

I stället för att använda [Azure-portalen](https://portal.azure.com)kan du utföra IoT Hub-stegen med Hjälp av Azure CLI. Mer information finns på Azure CLI-sidorna för [att skapa en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapa en IoT-enhet](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med att prova självstudien och testa dina resultat inaktiverar eller tar du bort resurser som du inte vill behålla.

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den.

1. Gå till logikappen.

2. På **bladet Översikt** väljer du **Ta bort** eller **Inaktivera**.

    Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

3. Gå till IoT-hubben.

4. Välj **Ta bort**på **bladet Översikt** .

    Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade.

5. Välj **Event Grid** i IoT-hubben.

6. Välj den händelseprenumeration som du vill ta bort.

7. Välj **Ta bort**.

Om du vill ta bort ett Azure Cosmos DB-konto från Azure-portalen högerklickar du på kontonamnet och klickar på **Ta bort konto**. Se detaljerade instruktioner för [att ta bort ett Azure Cosmos DB-konto](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att reagera på IoT Hub-händelser med hjälp av Händelserutnät för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md)

* [Prova självstudiekursen för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Läs mer om vad du kan göra med [Event Grid](../event-grid/overview.md)