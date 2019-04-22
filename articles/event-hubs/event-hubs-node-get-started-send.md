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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677909"
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
- **Skapa ett Event Hubs-namnområde och en event hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätt sedan med följande steg i den här självstudien. Hämta sedan anslutningssträngen för händelsehubbens namnområde genom att följa anvisningarna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.
- Klona den [GitHub exempellagringsplats](https://github.com/Azure/azure-event-hubs-node) på din dator. 


## <a name="send-events"></a>Skicka händelser
Det här avsnittet visar hur du skapar ett Node.js-program som skickar händelser till en händelsehubb. 

### <a name="install-nodejs-package"></a>Installera Node.js-paket
Installera Node.js-paket för Azure Event Hubs på din dator. 

```shell
npm install @azure/event-hubs
```

Om du inte har gjort det klonade Git-lagringsplats som vi nämnde i nödvändiga, ladda ned den [exempel](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) från GitHub. 

SDK: N som du har klonat innehåller flera exempel som visar dig hur du skickar händelser till en event hub med node.js. I den här snabbstarten använder du den **simpleSender.js** exempel. Om du vill se händelser som tas emot, öppnar du en annan terminal och ta emot händelser med hjälp av den [får exempel](event-hubs-node-get-started-receive.md).

1. Öppna projektet i Visual Studio Code. 
2. Skapa en fil med namnet **.env** under den **klienten** mapp. Kopiera och klistra in exemplet miljövariabler från den **sample.env** i rotmappen.
3. Konfigurera händelsehubbens anslutningssträng, händelsehubbens namn och slutpunkt för lagring. Mer information om hur du hämtar en anslutningssträng för en händelsehubb [hämta anslutningssträngen](event-hubs-create.md#create-an-event-hubs-namespace).
4. På din Azure-CLI, navigerar du till den **klienten** mappsökväg. Installera paket i noden och skapa projektet genom att köra följande kommandon:

    ```shell
    npm i
    npm run build
    ```
5. Börja skicka händelser genom att köra följande kommando: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Granska exempelkoden 
Granska exempelkoden i filen simpleSender.js att skicka händelser till en händelsehubb.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Kom ihåg att ange din miljövariabler innan du kör skriptet. Du kan konfigurera dem på kommandoraden som visas i följande exempel eller Använd den [dotenv paketet](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Ta emot händelser
Den här kursen visar hur du tar emot händelser från en händelsehubb med hjälp av Azure [EventProcessorHost](event-hubs-event-processor-host.md) i ett Node.js-program. EventProcessorHost (EPH) hjälper dig att effektivt ta emot händelser från en händelsehubb genom att skapa mottagare för alla partitioner i konsumentgrupp i en händelsehubb. Den mottagna meddelanden med jämna mellanrum i en Azure Storage Blob metadata kontrollpunkter. Den här metoden gör det enkelt att fortsätta att få meddelanden från där du slutade vid ett senare tillfälle.

Koden för den här snabbstarten finns på [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Klona Git-lagringsplats
Ladda ned eller klona den [exempel](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) från GitHub. 

### <a name="install-the-eventprocessorhost"></a>Installera EventProcessorHost
Installera EventProcessorHost för Event Hubs-modulen. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Ta emot händelser med hjälp av EventProcessorHost
SDK: N som du har klonat innehåller flera exempel som visar dig hur du tar emot händelser från en event hub med Node.js. I den här snabbstarten använder du den **singleEPH.js** exempel. Om du vill se händelser som tas emot, öppnar du en annan terminal och skicka händelser med hjälp av den [skicka prov](event-hubs-node-get-started-send.md).

1. Öppna projektet i Visual Studio Code. 
2. Skapa en fil med namnet **.env** under den **processor** mapp. Kopiera och klistra in exemplet miljövariabler från den **sample.env** i rotmappen.
3. Konfigurera händelsehubbens anslutningssträng, händelsehubbens namn och slutpunkt för lagring. Du kan kopiera anslutningssträngen för din händelsehubb från **anslutning anslutningssträng-primär** viktiga under **RootManageSharedAccessKey** på sidan Händelsehubb i Azure-portalen. Detaljerade anvisningar finns i [hämta anslutningssträngen](event-hubs-create.md#create-an-event-hubs-namespace).
4. På din Azure-CLI, navigerar du till den **processor** mappsökväg. Installera paket i noden och skapa projektet genom att köra följande kommandon:

    ```shell
    npm i
    npm run build
    ```
5. Ta emot händelser med dina värden för händelsebearbetning genom att köra följande kommando:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Granska exempelkoden 
Här är exempelkod för att ta emot händelser från en event hub med node.js. Du kan manuellt skapa en sampleEph.js-fil och kör den för att ta emot händelser till en händelsehubb. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Kom ihåg att ange din miljövariabler innan du kör skriptet. Du kan konfigurera det på kommandoraden som visas i följande exempel eller Använd den [dotenv paketet](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Du hittar fler exempel [här](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
- Titta på andra Node.js-exempel för Event Hubs på [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
