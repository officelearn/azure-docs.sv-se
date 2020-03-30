---
title: Skicka eller ta emot händelser från Azure Event Hubs med JavaScript (äldre)
description: Den här artikeln innehåller en genomgång för att skapa ett JavaScript-program som skickar/tar emot händelser till/från Azure Event Hubs med det gamla azure/event-hubs version 2-paketet.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 0a4b76bd1febca864cab6060fbdbd96dd0061cff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162624"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Snabbstart: Skicka händelser till eller ta emot händelser@azure/event-hubs från Azure Event Hubs med JavaScript ( version 2)
Den här snabbstarten visar hur du skapar JavaScript-program för att skicka händelser till och ta emot händelser från en händelsenav med javascript-paketet azure/event-hubs version 2. 

> [!WARNING]
> Den här snabbstarten använder det gamla azure/event-hubs version 2-paketet. En snabbstart som använder den **senaste versionen 5** av paketet finns i [Skicka och ta emot händelser med azure/eventhubs version 5](get-started-node-send-v2.md). Information om hur du flyttar programmet från det gamla paketet till det nya finns i [guiden för att migrera från azure/eventhubs version 1 till version 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Krav

Om du inte har gjort det tidigare i Azure Event Hubs läser du [översikt över eventhubbar](event-hubs-about.md) innan du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure-prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/) eller använda dina msdn-prenumerationsförmåner när du [skapar ett konto](https://azure.microsoft.com).
- Node.js version 8.x och högre. Ladda ner den senaste LTS-versionen från [https://nodejs.org](https://nodejs.org).
- Visual Studio-kod (rekommenderas) eller någon annan IDE
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Det första steget är att använda [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du proceduren i den [här artikeln](event-hubs-create.md)och fortsätter sedan med följande steg i den här självstudien. Hämta sedan anslutningssträngen för namnområdet för händelsehubben genom att följa instruktionerna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.


### <a name="install-npm-package"></a>Installera npm-paket
Om du vill installera [npm-paketet för eventhubbar](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)öppnar du en kommandotolk som har `npm` i sökvägen, ändrar katalogen till mappen där du vill ha dina exempel och kör sedan det här kommandot

```shell
npm install @azure/event-hubs@2
```

Om du vill installera [npm-paketet för händelsebehandlarens värd](https://www.npmjs.com/package/@azure/event-processor-host)kör du kommandot nedan i stället

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Skicka händelser

I det här avsnittet visas hur du skapar ett JavaScript-program som skickar händelser till en händelsehubb. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en `send.js` fil som heter och klistra in nedanstående kod i den. Hämta anslutningssträngen för namnområdet för händelsehubben genom att följa instruktionerna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnet på händelsehubben i ovanstående kod
4. Kör sedan `node send.js` kommandot i en kommandotolk för att köra den här filen. Detta skickar 100 händelser till din Event Hub

Grattis! Du har nu skickat händelser till en händelsehubb.


## <a name="receive-events"></a>Ta emot händelser

I det här avsnittet visas hur du skapar ett JavaScript-program som tar emot händelser från en enda partition i standardkonsumentgruppen i en händelsehubb. 

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en `receive.js` fil som heter och klistra in nedanstående kod i den.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Ange anslutningssträngen och namnet på händelsehubben i ovanstående kod.
4. Kör sedan `node receive.js` kommandot i en kommandotolk för att köra den här filen. Detta tar emot händelser från en av partitionerna i standardkonsumentgruppen i händelsehubben

Grattis! Du har nu fått händelser från händelsehubben.

## <a name="receive-events-using-event-processor-host"></a>Ta emot händelser med hjälp av värden för händelsebearbetning

Det här avsnittet visar hur du får händelser från en händelsehubb med hjälp av Azure [EventProcessorHost](event-hubs-event-processor-host.md) i ett JavaScript-program. EventProcessorHost (EPH) hjälper dig att effektivt ta emot händelser från en händelsehubb genom att skapa mottagare över alla partitioner i konsumentgruppen för en händelsehubb. Den vägleder metadata på mottagna meddelanden med jämna mellanrum i en Azure Storage Blob. Den här metoden gör det enkelt att fortsätta ta emot meddelanden från där du slutade vid ett senare tillfälle.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en `receiveAll.js` fil som heter och klistra in nedanstående kod i den.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Ange anslutningssträngen och namnet på händelsehubben i ovanstående kod tillsammans med anslutningssträngen för en Azure Blob Storage
4. Kör sedan `node receiveAll.js` kommandot i en kommandotolk för att köra den här filen.

Grattis! Du har nu fått händelser från händelsehubben med händelsebehandlare. Detta kommer att ta emot händelser från alla partitioner i standardkonsumentgruppen i händelsehubben

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
- Kolla in andra JavaScript-exempel för [eventhubbar](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) och [händelsebehandlare](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) på GitHub
