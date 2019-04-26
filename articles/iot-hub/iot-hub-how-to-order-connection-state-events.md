---
title: Ordna enhetshändelser anslutning från Azure IoT Hub med Azure Cosmos DB | Microsoft Docs
description: Den här artikeln beskriver hur du ordna och registrera enheten anslutningshändelser från Azure IoT Hub med Azure Cosmos DB för att upprätthålla senaste anslutningsstatus
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: ff8f8c6656c4cd095749b3e048c72572d113f1ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400583"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordna enheten anslutningshändelser från Azure IoT Hub med Azure Cosmos DB

Azure Event Grid kan du skapa händelse-baserade program och enkelt integrera IoT-händelser i dina affärslösningar. Den här artikeln beskriver hur du en konfiguration som kan användas för att spåra och lagra det senaste anslutningen enhetstillståndet i Cosmos DB. Vi använder sekvensnummer som är tillgängliga i händelserna enheten ansluten och enheten frånkopplad och lagra det senaste tillståndet i Cosmos DB. Vi ska använda en lagrad procedur är ett programlogik som körs mot en samling i Cosmos DB.

Sekvensnumret är en strängrepresentation av ett hexadecimalt tal. Du kan använda jämför sträng för att identifiera det större antalet. Om du konverterar den hexadecimal strängen kommer antalet att en 256-bitars tal. Sekvensnumret strikt ökar och den senaste händelsen kommer att ha högre nummer än andra händelser. Detta är användbart om du har ofta enheten ansluter och kopplar från och se till att endast den senaste händelsen används för att utlösa en underordnad åtgärd som Azure Event Grid har inte stöd för sorteringen av händelser.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett aktivt Azure-konto. Om du inte redan har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Ett aktivt Azure Cosmos DB SQL API-konto. Om du inte har skapat en ännu, se [skapa ett databaskonto](../cosmos-db/create-sql-api-dotnet.md#create-an-azure-cosmos-db-account) en genomgång.

* En samling i din databas. Se [Lägg till en samling](../cosmos-db/create-sql-api-dotnet.md#add-a-database-and-a-collection) en genomgång. När du skapar din samling, använda `/id` för Partitionsnyckeln.

* En IoT-hubb i Azure. Om du inte redan har skapat en hubb läser du genomgången i [Kom igång med IoT Hub](iot-hub-csharp-csharp-getstarted.md).

## <a name="create-a-stored-procedure"></a>Skapa en lagrad procedur

Först skapar du en lagrad procedur och ställas in att köra en logik som jämför sekvensnummer inkommande och registrerar den senaste händelsen per enhet i databasen.

1. Välj i din Cosmos DB SQL API **Datautforskaren** > **objekt** > **ny lagringsprocedur**.

   ![Skapa lagrad procedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Ange **LatestDeviceConnectionState** för lagrad procedur-ID och klistra in följande i den **lagringsprocedur brödtext**. Observera att den här koden bör ersätter eventuella befintliga koden i själva lagrad procedur. Den här koden har en rad per enhets-ID och registrerar senaste anslutningsstatus för enhets-ID genom att identifiera det högsta numret.

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

1. I den [Azure-portalen](https://portal.azure.com)väljer **+ skapa en resurs**väljer **integrering** och sedan **Logikapp**.

   ![Skapa en logikapp](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Ge logikappen ett namn som är unikt i din prenumeration och välj sedan samma prenumeration, resursgrupp och plats som din IoT-hubb.

   ![Ny logikapp](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Välj **skapa** att skapa logikappen.

   Du har nu skapat en Azure-resurs för din logikapp. När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE]
   > För att hitta och öppna logikappen igen, Välj **resursgrupper** och välj den resursgrupp som du använder för den här anvisningen. Välj sedan den nya logikappen. Då öppnas Logic App Designer.

4. I Logic App Designer, rulla åt höger tills du ser vanliga utlösare. Under **mallar**, Välj **tom Logikapp** så att du kan bygga din logikapp från början.

### <a name="select-a-trigger"></a>Välj en utlösare

En utlösare är en specifik händelse som startar din logikapp. I den här självstudien tar utlösaren som utlöser arbetsflödet emot en begäran via HTTP.

1. I kopplingar och utlösare sökfältet skriver **HTTP** och tryck på RETUR.

2. Välj **Begäran – När en HTTP-begäran tas emot** som utlösare.

   ![Välj utlösare för HTTP-begäranden](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Välj **Generera schemat genom att använda en exempelnyttolast**.

   ![Använd exempel för att skapa ett schema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Klistra in JSON-exempelnyttolast](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Ett popup-meddelande med texten **Remember to include a Content-Type header set to application/json in your request** (Glöm inte att ta med ett Content-Type-huvud konfigurerat till application/json i din begäran). Du kan ignorera det här förslaget och gå vidare till nästa avsnitt.

### <a name="create-a-condition"></a>Skapa ett villkor

I ditt logikapparbetsflöde att villkor köra specifika åtgärder efter skicka specifika villkoret. När villkoret är uppfyllt, kan du definiera en önskad åtgärd. Villkoret är att kontrollera om händelsetyp är enhet ansluten eller frånkopplad-enhet i den här självstudien. Åtgärden är att köra den lagrade proceduren i databasen.

1. Välj **+ nytt steg** sedan **inbyggda**, hitta och välj **villkor**. Klicka på **Välj ett värde** och en ruta visas som visar dynamiskt innehåll – de fält som kan väljas. Fyll i fälten som visas nedan för att utföra detta endast för enheten ansluten och enheten frånkopplad händelser:

   * Välj ett värde: **händelsetyp** – Välj det här alternativet från fälten i dynamiskt innehåll som visas när du klickar på det här fältet.
   * Ändra ”är lika med” att **slutar med**.
   * Välj ett värde: **nected**.

     ![Fyll villkor](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. I den **om värdet är true** dialogrutan klickar du på **Lägg till en åtgärd**.
  
   ![Lägg till åtgärd om värdet är true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Sök efter Cosmos DB och välj **Azure Cosmos DB – Kör lagrad procedur**

   ![Sök efter CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Fyll i **cosmosdb-connection** för den **anslutningsnamn** och markera posten i tabellen och välj sedan **skapa**. Du ser den **köra den lagrade proceduren** panelen. Ange värden för fälten:

   **Databas-ID**: ToDoList

   **Samlings-ID**: Objekt

   **Sproc-ID**: LatestDeviceConnectionState

5. Välj **Lägg till ny parameter**. I listrutan som visas markerar du kryssrutorna bredvid **partitionsnyckel** och **parametrar för den lagrade proceduren**, klicka på någon annanstans på skärmen, läggs ett fält för partitionsnyckelvärde och ett fält för parametrar för den lagrade proceduren.

   ![Fyll i logikappens](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Ange nu partitionsnyckelvärde och parametrar som visas nedan. Glöm inte att placera i hakparenteser och dubbla citattecken som visas. Du kanske måste klicka på **Lägg till dynamiskt innehåll** att hämta giltiga värden som du kan använda här.

   ![Fyll i logikappens](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Överst i rutan där det står **för var och en**under **Välj utdata från föregående steg**, kontrollera att den **brödtext** har valts.

   ![Fyll i logikapp för varje](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Spara din logikapp.

### <a name="copy-the-http-url"></a>Kopiera HTTP-URL:en

Kopiera den URL som din logikapp lyssnar på för en utlösare innan du lämnar Logic Apps Designer. Du använder den här URL:en för att konfigurera Event Grid.

1. Expandera konfigurationsrutan för utlösaren **När en HTTP-begäran tas emot** genom att klicka på den.

2. Kopiera värdet för **HTTP POST-URL** genom att välja kopieringsknappen bredvid det.

   ![Kopiera HTTP POST-URL:en](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Spara den här URL:en så att du kan referera till den i nästa avsnitt.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurera prenumerationen för IoT Hub-händelser

I det här avsnittet ska du konfigurera din IoT-hubb så att den publicerar händelser när de inträffar.

1. Gå till din IoT-hubb på Azure Portal.

2. Välj **Händelser**.

   ![Öppna Event Grid-informationen](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Välj **+ händelseprenumeration**.

   ![Skapa ny händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Fyll i **händelse prenumerationsinformation**: Ange ett beskrivande namn och välj **Grid Händelseschema**.

5. Fyll i den **händelsetyper** fält. Avmarkera **prenumerera på alla händelsetyper** och välj **enheten ansluten** och **enheten frånkopplad** på menyn.

   ![Ange händelsetyper efter](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. För **slutpunktsinformation**, Välj typ av slutpunkt som **Webhook** och klicka på Välj slutpunkt och klistra in den URL som du kopierade från din logikapp och bekräfta valet.

   ![Välj slutpunkts-url](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

7. Formuläret bör nu se ut ungefär så här:

   ![Exempelformulär för händelseprenumeration](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Spara händelseprenumerationen genom att välja **Skapa**.

## <a name="observe-events"></a>Se händelser

Nu när din händelseprenumeration är konfigurerat kan vi testa genom att ansluta en enhet.

### <a name="register-a-device-in-iot-hub"></a>Registrera en enhet i IoT Hub

1. Välj **IoT-enheter** från din IoT-hubb.

2. Välj **+ Lägg till** överst i fönstret.

3. Ange `Demo-Device-1` för **Enhets-ID**.

4. Välj **Spara**.

5. Du kan lägga till flera enheter med olika enhets-ID.

   ![Enheter som har lagts till hubben](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klicka på enheten igen. nu fyllas anslutningssträngar och nycklar i. Kopiera den **anslutningssträng – primärnyckel** för senare användning.

   ![ConnectionString för enhet](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

HostName=test-eventgrid-hub.azure-devices.net;DeviceId=Demo-Device-1;SharedAccessKey=cv8uPNixe7E2R9EHtimoY/PlJfBV/lOYCMajVOp/Cuw=

### <a name="start-raspberry-pi-simulator"></a>Starta simulatorn Raspberry Pi

1. Nu ska vi använda Raspberry Pi web simulatorn för att simulera enhetsanslutning.

[Starta simulatorn Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Kör ett exempelprogram på web-simulator Raspberry Pi

Detta utlöser en ansluten enhet-händelse.

1. I området för kodning, ersätter du platshållaren i rad 15 med Azure IoT Hub enhetens anslutningssträng som du sparade i föregående avsnitt.

   ![Klistra in enhetens anslutningssträng](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Kör programmet genom att välja **kör**.

Du ser något som liknar följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

   ![Köra programmet](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicka på **stoppa** att stoppa simulator och utlöser en **enheten frånkopplad** händelse.

Du har nu kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb.

### <a name="observe-events-in-cosmos-db"></a>Se händelser i Cosmos DB

Du kan se resultatet av den lagrade proceduren som körs i ditt Cosmos DB-dokument. Här är hur det ser ut. Varje rad innehåller det senaste anslutningen för enhetstillståndet per enhet.

   ![Så här resultatet](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Istället för att använda den [Azure-portalen](https://portal.azure.com), du kan utföra följande steg för IoT Hub som använder Azure CLI. Mer information finns i Azure CLI-sidor för [skapa en händelseprenumeration](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) och [skapar en IoT-enhet](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudiekursen användes resurser som medför kostnader för din Azure-prenumeration. När du är klar med kursen och har testat resultaten bör du därför inaktivera eller ta bort resurser som du inte vill behålla.

Om du inte vill förlora det arbete du gjort i logikappen inaktiverar du den i stället för att ta bort den.

1. Gå till logikappen.

2. På den **översikt** bladet väljer **ta bort** eller **inaktivera**.

    Varje prenumeration kan ha en kostnadsfri IoT-hubb. Om du har skapat en kostnadsfri hubb för den här självstudiekursen behöver du inte ta bort den för att undvika kostnader.

3. Gå till IoT-hubben.

4. På den **översikt** bladet väljer **ta bort**.

    Även om du behåller din IoT-hubb kanske du vill ta bort händelseprenumerationen som du skapade.

5. Välj **Event Grid** i IoT-hubben.

6. Välj den händelseprenumeration som du vill ta bort.

7. Välj **Ta bort**.

Om du vill ta bort ett Azure Cosmos DB-konto från Azure-portalen, högerklickar du på namnet på kontot och klicka på **ta bort konto**. Se detaljerade anvisningar för hur [tar bort ett Azure Cosmos DB-konto](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder](../iot-hub/iot-hub-event-grid.md)

* [Prova guiden för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Läs om vad du kan göra med [Event Grid](../event-grid/overview.md)