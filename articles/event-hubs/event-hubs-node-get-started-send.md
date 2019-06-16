---
title: Skicka och ta emot händelser med hjälp av Node.js – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Node.js-program som skickar händelser från Azure Event Hubs.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539335"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs med hjälp av Node.js

Azure Event Hubs är en Big Data som strömmas-plattformen och händelsen händelseinmatningstjänst som kan ta emot och bearbeta miljontals händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skapar en Node.js-program att skicka händelser till eller ta emot händelser från en event hub.

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org).
- Visual Studio Code (rekommenderas) eller andra IDE
- **Skapa ett Event Hubs-namnområde och en event hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätt sedan med följande steg i den här självstudien. Hämta sedan anslutningssträngen för händelsehubbens namnområde genom att följa anvisningarna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen för senare i den här självstudien.


### <a name="install-npm-package"></a>Installera npm-paket
Installera den [npm-paket för Event Hubs](https://www.npmjs.com/package/@azure/event-hubs), öppna en kommandotolk som har `npm` i sökvägen, ändra katalogen till mappen där du vill ha exempel och kör sedan det här kommandot

```shell
npm install @azure/event-hubs
```

Installera den [npm-paket för värd för händelsebearbetning](https://www.npmjs.com/package/@azure/event-processor-host)kör i kommandot nedan i stället

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Skicka händelser

Det här avsnittet visar hur du skapar ett Node.js-program som skickar händelser till en händelsehubb. 

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `send.js` och klistra in den nedan kod till den.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. Ange anslutningssträngen och namnet på din Event Hub i koden ovan
4. Kör sedan kommandot `node send.js` i Kommandotolken och kör den här filen. 100 händelser skickas till din Event Hub

Grattis! Du har nu skickat händelser till en händelsehubb.


## <a name="receive-events"></a>Ta emot händelser

Det här avsnittet visar hur du skapar ett Node.js-program som tar emot händelser från en enda partition på förinställd konsumentgrupp i en händelsehubb. 

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `receive.js` och klistra in den nedan kod till den.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
3. Ange anslutningssträngen och namnet på din Event Hub i koden ovan.
4. Kör sedan kommandot `node receive.js` i Kommandotolken och kör den här filen. Detta visas händelser från någon av partitionerna i förinställd konsumentgrupp i din Event Hub

Grattis! Du har nu fått händelser från event hub.

## <a name="receive-events-using-event-processor-host"></a>Ta emot händelser med värden för händelsebearbetning

Det här avsnittet visas hur du tar emot händelser från en händelsehubb med hjälp av Azure [EventProcessorHost](event-hubs-event-processor-host.md) i ett Node.js-program. EventProcessorHost (EPH) hjälper dig att effektivt ta emot händelser från en händelsehubb genom att skapa mottagare för alla partitioner i konsumentgrupp i en händelsehubb. Den mottagna meddelanden med jämna mellanrum i en Azure Storage Blob metadata kontrollpunkter. Den här metoden gör det enkelt att fortsätta att få meddelanden från där du slutade vid ett senare tillfälle.

1. Öppna din favoritredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com). 
2. Skapa en fil med namnet `receiveAll.js` och klistra in den nedan kod till den.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Ange anslutningssträngen och namnet på din Event Hub i koden ovan tillsammans med anslutningssträngen för Azure Blob Storage
4. Kör sedan kommandot `node receiveAll.js` i Kommandotolken och kör den här filen.

Grattis! Du har nu fått händelser från event hub med värden för händelsebearbetning. Detta kan ta emot händelser från alla partitioner i förinställd konsumentgrupp i din Event Hub

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
- Titta på andra Node.js-exempel för [Händelsehubbar](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) och [Eventprocessorhost](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) på GitHub
