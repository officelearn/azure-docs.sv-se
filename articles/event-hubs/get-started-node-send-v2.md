---
title: Skicka och ta emot händelser med Node. js – Azure Event Hubs
description: Den här artikeln innehåller en genomgång för att skapa ett Node.js-program som skickar händelser från Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: 9ea6febc781422a72ac6547338c8b21239331083
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942521"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs med Node. js 

Azure Event Hubs är en stor data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skapar Node. js-program för att skicka händelser till eller ta emot händelser från en händelsehubben.

> [!IMPORTANT]
> I den här snabb starten används version 5 av Azure Event Hubs Java Script SDK. En snabb start som använder den gamla version 2 av Java Script SDK finns i [den här artikeln](event-hubs-node-get-started-send.md). Om du använder version 2 av SDK rekommenderar vi att du migrerar din kod till den senaste versionen. Mer information finns i [migreringsguiden](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (rekommenderas) eller andra IDE
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namn område och en Event Hub följer du stegen i [den här artikeln](event-hubs-create.md)och fortsätter sedan med följande steg i den här självstudien. Hämta sedan anslutnings strängen för Event Hub-namnområdet genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.


### <a name="install-npm-packages"></a>Installera NPM-paket
Om du vill installera [NPM-paketet för Event Hubs](https://www.npmjs.com/package/@azure/event-hubs)öppnar du en kommando tolk som har `npm` i sökvägen, ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan kommandot

```shell
npm install @azure/event-hubs
```

Du måste installera två fler paket för den mottagande sidan. I den här snabb starten använder du Azure Blob Storage för att spara kontroll punkter så att programmet inte läser de händelser som redan har lästs. Den kontroll punkten tar emot metadata på mottagna meddelanden med jämna mellanrum i en blob. Den här metoden gör det enkelt att fortsätta att få meddelanden från där du slutade vid ett senare tillfälle.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Skicka händelser

Det här avsnittet visar hur du skapar ett Node. js-program som skickar händelser till en händelsehubben. 

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `send.js` och klistra in följande kod i den. 

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");
    
    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {
    
      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);
    
      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    
    
      // send the batch to the event hub
      await producer.sendBatch(batch);
    
      // close the producer client
      await producer.close();
    
      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Glöm inte att byta namn på **anslutnings strängen** och **händelsehubben** i koden. 
5. Kör kommandot `node send.js` för att köra den här filen. Då skickas en batch med tre händelser till händelsehubben
6. I Azure Portal kan du kontrol lera att händelsehubben har tagit emot meddelandena. Växla till vyn **meddelanden** i avsnittet **mått** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits. 

    [![verifiera att händelsehubben tog emot meddelanden](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)

Grattis! Du har nu skickat händelser till en händelsehubben. 


## <a name="receive-events"></a>Ta emot händelser 
I det här avsnittet visas hur du tar emot händelser från en Event Hub genom att använda en Azure Blob-kontrollpunkt i ett Node. js-program. Den mottagna meddelanden med jämna mellanrum i en Azure Storage Blob metadata kontrollpunkter. Den här metoden gör det enkelt att fortsätta att få meddelanden från där du slutade vid ett senare tillfälle.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en BLOB-behållare
Följ dessa steg om du vill skapa en Azure Storage-konto en BLOB-behållare. 

1. [Skapa ett Azure Storage konto](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Anteckna anslutnings sträng och behållar namn. Du kommer att använda dem i Receive-koden. 

### <a name="write-code-to-receive-events"></a>Skriva kod för att ta emot händelser

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `receive.js` och klistra in följande kod i den. Se kod kommentarer för mer information. 
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
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);
    
      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);
    
      // subscribe for the events and specify handlers for processing the events and errors. 
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint 
            await context.updateCheckpoint(events[events.length - 1]);
          },
    
          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );
        
      // after 30 seconds, stop processing
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
3. Glöm inte att ange **följande värden** i koden: 
    - Anslutnings sträng till Event Hubs namn område
    - Namn på händelsehubben
    - Anslutnings sträng till Azure Storage konto
    - Namn på BLOB-behållaren
5. Kör sedan kommandot `node receive.js` i en kommando tolk för att köra den här filen. Du bör se meddelanden om mottagna händelser i fönstret. 

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Grattis! Du har nu tagit emot händelser från händelsehubben. Mottagar programmet tar emot händelser från alla partitioner i standard konsument gruppen i händelsehubben

## <a name="next-steps"></a>Nästa steg
Kolla in de här exemplen på GitHub:

- [JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)