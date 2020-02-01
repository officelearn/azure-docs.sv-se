---
title: Skicka eller ta emot händelser från Azure Event Hubs med Node. js (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett Node. js-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av de senaste Azure/Event-Hub version 5-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906365"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Skicka händelser till eller ta emot händelser från Event Hub med hjälp av Node. js (Azure/Event-Hub version 5)

Azure Event Hubs är en stor data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Event Hub kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en Event Hub kan transformeras och lagras med hjälp av en analys av real tids analys eller batch-/minnes kort. Mer information finns i [Event Hubs översikt](event-hubs-about.md) och [Event Hubs funktioner](event-hubs-features.md).

I den här snabb starten beskrivs hur du skapar Node. js-program som kan skicka händelser till eller ta emot händelser från en händelsehubben.

> [!IMPORTANT]
> I den här snabb starten används version 5 av Azure Event Hubs JavaScript SDK. En snabb start som använder version 2 av JavaScript SDK finns i [den här artikeln](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.  
- Node. js version 8. x eller senare. Hämta den senaste [versionen av LTS (Long-term support)](https://nodejs.org).  
- Visual Studio Code (rekommenderas) eller andra Integrated Development Environment (IDE).  
- Ett aktivt Event Hubs namn område och händelsehubben. Gör så här för att skapa dem: 

   1. Skapa ett namn område av typen *Event Hubs*i [Azure Portal](https://portal.azure.com)och hämta sedan de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. 
   1. Om du vill skapa namn området och händelsehubben följer du anvisningarna i [snabb start: skapa en Event Hub med hjälp av Azure Portal](event-hubs-create.md).
   1. Fortsätt genom att följa anvisningarna i den här snabb starten. 
   1. Följ anvisningarna i [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)för att hämta anslutnings strängen för Event Hub-namnområdet. Registrera anslutnings strängen som ska användas senare i den här snabb starten.

### <a name="install-the-npm-package"></a>Installera NPM-paketet
Om du vill installera [NPM-paketet (Node Package Manager) för Event Hubs](https://www.npmjs.com/package/@azure/event-hubs)öppnar du en kommando tolk som har *NPM* i sökvägen, ändrar katalogen till den mapp där du vill behålla dina exempel och kör sedan kommandot:

```shell
npm install @azure/event-hubs
```

Du måste installera två fler paket för den mottagande sidan. I den här snabb starten använder du Azure Blob Storage för att bevara kontroll punkter så att programmet inte läser de händelser som redan har lästs. Den utför kontroll punkter för metadata på mottagna meddelanden med jämna mellanrum i en blob. Den här metoden gör det enkelt att fortsätta att ta emot meddelanden senare, där du slutade.

Kör följande kommandon:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Skicka händelser

I det här avsnittet skapar du ett Node. js-program som skickar händelser till en Event Hub.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil med namnet *send. js*och klistra in följande kod i den:

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. I koden använder du verkliga värden för att ersätta följande:
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. Kör `node send.js` för att köra den här filen. Det här kommandot skickar en batch med tre händelser till händelsehubben.
1. I Azure Portal kontrollerar du att händelsehubben har tagit emot meddelandena. I avsnittet **mått** växlar du till vyn **meddelanden** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits.

    [![verifiera att händelsehubben tog emot meddelanden](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare information kommentarer, går du till [sidan GitHub sendEvents. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Grattis! Du har nu skickat händelser till en händelsehubben.


## <a name="receive-events"></a>Ta emot händelser
I det här avsnittet får du händelser från en händelsehubben genom att använda en Azure Blob Storage-kontrollpunkt i ett Node. js-program. Den utför kontroll punkter för metadata på mottagna meddelanden med jämna mellanrum i en Azure Storage-blob. Den här metoden gör det enkelt att fortsätta att ta emot meddelanden senare, där du slutade.

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure Storage-konto och en BLOB-behållare
Om du vill skapa ett Azure Storage-konto och en BLOB-behållare i det gör du följande:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Skapa en BLOB-behållare i lagrings kontot](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Se till att du registrerar anslutnings strängen och behållar namnet för senare användning i mottagar koden.

### <a name="write-code-to-receive-events"></a>Skriva kod för att ta emot händelser

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil med namnet *Receive. js*och klistra in följande kod i den:

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. I koden använder du verkliga värden för att ersätta följande värden:
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. Kör `node receive.js` i en kommando tolk för att köra den här filen. Fönstret ska visa meddelanden om mottagna händelser.

    > [!NOTE]
    > För den fullständiga käll koden, inklusive ytterligare information kommentarer, går du till [sidan GitHub receiveEventsUsingCheckpointStore. js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Grattis! Du har nu tagit emot händelser från händelsehubben. Mottagar programmet tar emot händelser från alla partitioner i standard gruppen för användare i händelsehubben.

## <a name="next-steps"></a>Nästa steg
Kolla in de här exemplen på GitHub:

- [JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
