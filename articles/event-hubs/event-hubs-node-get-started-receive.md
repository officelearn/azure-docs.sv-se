---
title: Ta emot händelser med Node.js – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Node.js-program som tar emot händelser från Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d13f97ff7ec25fd638b92dc1c057afcaeecc2bf9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434077"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Ta emot händelser från Azure Event Hubs med hjälp av Node.js

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av. Du kan också samla in händelsedata i Azure Storage eller Azure Data Lake Store innan den bearbetas.  

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar](event-hubs-about.md).

Den här kursen visar hur du tar emot händelser från en händelsehubb med hjälp av Azure [EventProcessorHost](event-hubs-event-processor-host.md) i ett Node.js-program. EventProcessorHost (EPH) hjälper dig att effektivt ta emot händelser från en händelsehubb genom att skapa mottagare för alla partitioner i konsumentgrupp i en händelsehubb. Den mottagna meddelanden med jämna mellanrum i en Azure Storage Blob metadata kontrollpunkter. Den här metoden gör det enkelt att fortsätta att få meddelanden från där du slutade vid ett senare tillfälle.

Koden för den här snabbstarten finns på [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Se den här artikeln för att skicka händelser till Event Hubs med hjälp av Node.js: [Skicka händelser till Azure Event Hubs med hjälp av Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Node.js-version 8.x och högre. Hämta den senaste versionen LTS från [ https://nodejs.org ](https://nodejs.org). Använd inte äldre LTS version av node.js. 
- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

## <a name="create-a-namespace-and-event-hub"></a>Skapa ett namnområde och en händelsehubb
Det första steget är att använda Azure portal för att skapa ett namnområde för Event Hubs med en händelsehubb. Om du inte har en befintlig, du kan skapa dessa enheter genom att följa instruktionerna i [skapa ett Event Hubs-namnområde och en event hub med Azure-portalen](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare
Om du vill använda EventProcessorHost, måste du ha ett Azure Storage-konto. Tillståndsinformationen som lån på partitioner och kontrollpunkter i händelseströmmen delas mellan mottagare med hjälp av en Azure Storage-behållare. Du kan skapa ett Azure Storage-konto genom att följa instruktionerna i [i den här artikeln](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Klona Git-lagringsplats
Ladda ned eller klona den [exempel](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) från GitHub. 

## <a name="install-the-eventprocessorhost"></a>Installera EventProcessorHost
Installera EventProcessorHost för Event Hubs-modulen. 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Ta emot händelser med hjälp av EventProcessorHost
SDK: N som du har klonat innehåller flera exempel som visar dig hur du tar emot händelser från en event hub med Node.js. I den här snabbstarten använder du den **singleEPH.js** exempel. Om du vill se händelser som tas emot, öppnar du en annan terminal och skicka händelser med hjälp av den [skicka prov](event-hubs-node-get-started-send.md).

1. Öppna projektet i Visual Studio Code. 
2. Skapa en fil med namnet **.env** under den **processor** mapp. Kopiera och klistra in exemplet miljövariabler från den **sample.env** i rotmappen.
3. Konfigurera händelsehubbens anslutningssträng, händelsehubbens namn och slutpunkt för lagring. Du kan kopiera anslutningssträngen för din händelsehubb från **anslutning anslutningssträng-primär** viktiga under **RootManageSharedAccessKey** på sidan Händelsehubb i Azure-portalen. Detaljerade anvisningar finns i [hämta anslutningssträngen](event-hubs-create.md#create-an-event-hubs-namespace).
4. På din Azure-CLI, navigerar du till den **processor** mappsökväg. Installera paket i noden och skapa projektet genom att köra följande kommandon:

    ```nodejs
    npm i
    npm run build
    ```
5. Ta emot händelser med dina värden för händelsebearbetning genom att köra följande kommando:

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>Granska exempelkoden 
Här är exempelkod för att ta emot händelser från en event hub med node.js. Du kan manuellt skapa en sampleEph.js-fil och kör den för att ta emot händelser till en händelsehubb. 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Du hittar fler exempel [här](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Node.js-program som har fått meddelanden från en event hub. Läs hur du skickar händelser till en event hub med Node.js i [skicka händelser från event hub - Node.js](event-hubs-node-get-started-send.md).

<!-- Links -->
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
