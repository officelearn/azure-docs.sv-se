---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java Script (bakåtkompatibelt)
description: Den här artikeln innehåller en genom gång av hur du skapar ett JavaScript-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det gamla Azure/Event-Hub version 2-paketet.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ffa6b821d617731bd6ee5cbb9bec8bd6d856a7a6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414130"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Snabb start: skicka händelser till eller ta emot händelser från Azure Event Hubs med hjälp av Java Script ( @azure/event-hubs version 2)
Den här snabb starten visar hur du skapar JavaScript-program för att skicka händelser till och ta emot händelser från en händelsehubben med hjälp av JavaScript-paketet Azure/Event-Hubbs version 2. 

> [!WARNING]
> Den här snabb starten använder det gamla Azure/Event-Hub version 2-paketet. En snabb start som använder den senaste **versionen 5** av paketet finns i [skicka och ta emot händelser med hjälp av Azure/eventhubs version 5](get-started-node-send-v2.md). Information om hur du flyttar ditt program från med det gamla paketet till New finns i [guiden för att migrera från Azure/eventhubs version 1 till version 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Förutsättningar

Om du är nybörjare på Azure Event Hubs, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- Node.js version 8. x och högre. Hämta den senaste versionen av LTS från [https://nodejs.org](https://nodejs.org) .
- Visual Studio Code (rekommenderas) eller någon annan IDE
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namn område och en Event Hub följer du stegen i [den här artikeln](event-hubs-create.md)och fortsätter sedan med följande steg i den här självstudien. Hämta sedan anslutnings strängen för Event Hub-namnområdet genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.


### <a name="install-npm-package"></a>Installera NPM-paket
Om du vill installera [NPM-paketet för Event Hubs](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0)öppnar du en kommando tolk som har `npm` i sökvägen och ändrar katalogen till den mapp där du vill ha dina exempel och kör sedan det här kommandot

```shell
npm install @azure/event-hubs@2
```

Om du vill installera [NPM-paketet för Event processor-värden](https://www.npmjs.com/package/@azure/event-processor-host)kör du kommandot nedan i stället

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Skicka händelser

Det här avsnittet visar hur du skapar ett JavaScript-program som skickar händelser till en händelsehubben. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `send.js` och klistra in nedanstående kod i den. Hämta anslutnings strängen för Event Hub-namnområdet genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

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
3. Ange anslutnings strängen och namnet på Händelsehubben i ovanstående kod
4. Kör sedan kommandot `node send.js` i en kommando tolk för att köra den här filen. Detta kommer att skicka 100-händelser till Händelsehubben

Grattis! Du har nu skickat händelser till en händelsehubben.


## <a name="receive-events"></a>Ta emot händelser

Det här avsnittet visar hur du skapar ett JavaScript-program som tar emot händelser från en enda partition av standard konsument gruppen i en Event Hub. 

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `receive.js` och klistra in nedanstående kod i den.
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
3. Ange anslutnings strängen och namnet på Händelsehubben i ovanstående kod.
4. Kör sedan kommandot `node receive.js` i en kommando tolk för att köra den här filen. Detta tar emot händelser från en av partitionerna i standard konsument gruppen i Händelsehubben

Grattis! Du har nu tagit emot händelser från händelsehubben.

## <a name="receive-events-using-event-processor-host"></a>Ta emot händelser med hjälp av värden för händelsebearbetning

I det här avsnittet visas hur du tar emot händelser från en Event Hub med hjälp av Azure- [EventProcessorHost](event-hubs-event-processor-host.md) i ett JavaScript-program. EventProcessorHost (EPH) hjälper dig att effektivt ta emot händelser från en händelsehubben genom att skapa mottagare över alla partitioner i konsument gruppen för en Event Hub. Den kontroll punkten tar emot metadata på mottagna meddelanden med jämna mellanrum i en Azure Storage Blob. Den här metoden gör det enkelt att fortsätta ta emot meddelanden där du slutade vid ett senare tillfälle.

1. Öppna din favorit redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `receiveAll.js` och klistra in nedanstående kod i den.
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
3. Ange anslutnings strängen och namnet på Händelsehubben i ovanstående kod tillsammans med anslutnings strängen för en Azure-Blob Storage
4. Kör sedan kommandot `node receiveAll.js` i en kommando tolk för att köra den här filen.

Grattis! Du har nu tagit emot händelser från händelsehubben med händelse bearbetnings värd. Detta tar emot händelser från alla partitioner i standard konsument gruppen i Händelsehubben

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
- Kolla in andra JavaScript-exempel för [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) och [händelse bearbetnings värd](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) på GitHub
