---
title: Skicka eller ta emot händelser från Azure Event Hubs med JavaScript (senaste)
description: Den här artikeln innehåller en genomgång för att skapa ett JavaScript-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste azure/event-hubs version 5-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 063f2e1005db177420da7f81b799ab01fd212d7e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478472"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-javascript--azureevent-hubs-version-5"></a>Skicka händelser till eller ta emot händelser från händelsehubbar med hjälp av JavaScript (azure/event-hubs version 5)
Den här snabbstarten visar hur du skickar händelser till och ta emot händelser från en händelsenav med **azure/event-hubs version 5 JavaScript-paketet.** 

> [!IMPORTANT]
> Den här snabbstarten använder det senaste azure/event-hubs version 5-paketet. En snabbstart som använder det gamla azure/event-hubs version 2-paketet finns i [Skicka och ta emot händelser med azure/event-hubs version 2](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Krav
Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- Node.js version 8.x eller senare. Ladda ner den senaste [lts-versionen (Long-term support).](https://nodejs.org)  
- Visual Studio Code (rekommenderas) eller någon annan integrerad utvecklingsmiljö (IDE).  
- Ett aktivt namnområde för händelsehubbar och händelsenav. Så här skapar du dem: 

   1. Skapa ett namnområde av typen *Event Hubs*i [Azure-portalen](https://portal.azure.com)och hämta sedan de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. 
   1. Om du vill skapa namnområdes- och händelsehubben följer du instruktionerna i [Snabbstart: Skapa en händelsehubb med hjälp av Azure-portalen](event-hubs-create.md).
   1. Fortsätt genom att följa instruktionerna i den här snabbstarten. 
   1. Om du vill hämta anslutningssträngen för namnområdet Event Hub följer du instruktionerna i [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Spela in anslutningssträngen som ska användas senare i den här snabbstarten.
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutningssträngen för namnområdet Event Hubs** genom att följa instruktionerna från artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutningssträngen senare i den här snabbstarten.

### <a name="install-the-npm-package"></a>Installera npm-paketet
Om du vill installera [nodpakethanterarens (npm) paket för eventhubbar](https://www.npmjs.com/package/@azure/event-hubs)öppnar du en kommandotolk som har *npm* i sökvägen, ändrar katalogen till mappen där du vill behålla exemplen och kör sedan det här kommandot:

```shell
npm install @azure/event-hubs
```

För den mottagande sidan måste du installera ytterligare två paket. I den här snabbstarten använder du Azure Blob-lagring för att bevara kontrollpunkter så att programmet inte läser de händelser som det redan har läst. Den utför metadata kontrollpunkter på mottagna meddelanden med jämna mellanrum i en blob. Den här metoden gör det enkelt att fortsätta ta emot meddelanden senare från där du slutade.

Kör följande kommandon:

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Skicka händelser

I det här avsnittet skapar du ett JavaScript-program som skickar händelser till en händelsehubb.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil som heter *send.js*och klistra in följande kod i den:

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
1. Kör `node send.js` för att köra den här filen. Det här kommandot skickar en grupp med tre händelser till händelsehubben.
1. Kontrollera att händelsehubben har tagit emot meddelandena i Azure-portalen. Växla till **meddelandevyn** i avsnittet **Mått.** Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har tagits emot.

    [![Kontrollera att händelsehubben har tagit emot meddelandena](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > För den fullständiga källkoden, inklusive ytterligare informationskommentarer, gå till [sidan GitHub sendEvents.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js).

Grattis! Du har nu skickat händelser till en händelsehubb.


## <a name="receive-events"></a>Ta emot händelser
I det här avsnittet får du händelser från en händelsehubb med hjälp av ett Azure Blob storage checkpoint-arkiv i ett JavaScript-program. Den utför metadatakontrollpunkter på mottagna meddelanden med jämna mellanrum i en Azure Storage-blob. Den här metoden gör det enkelt att fortsätta ta emot meddelanden senare från där du slutade.

> [!NOTE]
> Om du körs på Azure Stack Hub kan den plattformen ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga på Azure. Om du till exempel kör [på Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagringstjänsten version 2017-11-09. I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod för att rikta lagringstjänstens API-version 2017-11-09. Ett exempel på hur du inriktar dig på en viss Storage API-version finns i [JavaScript-](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) och [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) på GitHub. Mer information om Azure Storage-tjänstversionerna som stöds på Azure Stack Hub finns i [Azure Stack Hub storage: Skillnader och överväganden](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Skapa ett Azure-lagringskonto och en blob-behållare
Så här skapar du ett Azure-lagringskonto och en blob-behållare i det:

1. [Skapa ett Azure-lagringskonto](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [Skapa en blob-container i lagringskontot](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Var noga med att registrera anslutningssträngen och behållarnamnet för senare användning i mottagningskoden.

### <a name="write-code-to-receive-events"></a>Skriv kod för att ta emot händelser

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com).
1. Skapa en fil som heter *receive.js*och klistra in följande kod i den:

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
1. Kör `node receive.js` i en kommandotolk för att köra den här filen. Fönstret ska visa meddelanden om mottagna händelser.

    > [!NOTE]
    > För den fullständiga källkoden, inklusive ytterligare informationskommentarer, gå till [sidan GitHub receiveEventsUsingCheckpointStore.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Grattis! Du har nu fått händelser från din händelsehubb. Mottagarprogrammet tar emot händelser från alla partitioner i standardkonsumentgruppen i händelsehubben.

## <a name="next-steps"></a>Nästa steg
Kolla in dessa exempel på GitHub:

- [JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
